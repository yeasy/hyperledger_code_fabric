### handler.go

主要提供：

* Handler 结构体：对所关联的链码容器进行相应，内部有一个状态机。
* HandleChaincodeStream() 方法：对外提供初始化的 Handler 结构体，并进入循环，不断接收来自链码容器的消息。

#### Handler 结构体
Peer 侧会为每一个 chaincode 维护一个 Handler 结构，具体响应所绑定的 chaincode 容器过来的各种消息，通过内部状态机进行处理。

Handler 结构实现了 MessageHandler 接口，主要提供一个 `HandleMessage(msg *pb.ChaincodeMessage) error` 方法，作为处理各个消息的入口方法。

```go
type Handler struct {
	sync.RWMutex
	//peer to shim grpc serializer. User only in serialSend
	serialLock  sync.Mutex
	ChatStream  ccintf.ChaincodeStream
	FSM         *fsm.FSM
	ChaincodeID *pb.ChaincodeID
	ccInstance  *sysccprovider.ChaincodeInstance

	chaincodeSupport *ChaincodeSupport
	registered       bool
	readyNotify      chan bool
	// Map of tx txid to either invoke tx. Each tx will be
	// added prior to execute and remove when done execute
	txCtxs map[string]*transactionContext

	txidMap map[string]bool

	// used to do Send after making sure the state transition is complete
	nextState chan *nextStateInfo

	policyChecker policy.PolicyChecker
}
```

chaincode 容器启动后，会调用到服务端的 Register() 方法，该方法进一步调用到 HandleChaincodeStream()，创建 Handler 结构体，进入接收消息循环。

```go
// HandleChaincodeStream Main loop for handling the associated Chaincode stream
func HandleChaincodeStream(chaincodeSupport *ChaincodeSupport, ctxt context.Context, stream ccintf.ChaincodeStream) error {
	deadline, ok := ctxt.Deadline()
	chaincodeLogger.Debugf("Current context deadline = %s, ok = %v", deadline, ok)
	handler := newChaincodeSupportHandler(chaincodeSupport, stream)
	return handler.processStream()
}
```

newChaincodeSupportHandler 方法中会初始化 FSM。

之后，调用 handler.processStream() 进入对来自 chaincode 容器消息处理的主循环。	

#### handler.processStream() 主消息循环

Peer 侧维护一个到 cc 的双向流，循环处理消息。主要在 `func (handler *Handler) processStream() error ` 方法中（cc 到 peer 注册后会自动调用到该方法）。

主循环过程代码如下：

```
	for {
		in = nil
		err = nil
		nsInfo = nil
		if recv {
			recv = false
			go func() {
				var in2 *pb.ChaincodeMessage
				in2, err = handler.ChatStream.Recv()
				msgAvail <- in2
			}()
		}
		select {
		case sendErr := <-errc:
			if sendErr != nil {
				return sendErr
			}
			//send was successful, just continue
			continue
		case in = <-msgAvail:
			// Defer the deregistering of the this handler.
			if err == io.EOF {
				chaincodeLogger.Debugf("Received EOF, ending chaincode support stream, %s", err)
				return err
			} else if err != nil {
				chaincodeLogger.Errorf("Error handling chaincode support stream: %s", err)
				return err
			} else if in == nil {
				err = fmt.Errorf("Received nil message, ending chaincode support stream")
				chaincodeLogger.Debug("Received nil message, ending chaincode support stream")
				return err
			}
			chaincodeLogger.Debugf("[%s]Received message %s from shim", shorttxid(in.Txid), in.Type.String())
			if in.Type.String() == pb.ChaincodeMessage_ERROR.String() {
				chaincodeLogger.Errorf("Got error: %s", string(in.Payload))
			}

			// we can spin off another Recv again
			recv = true

			if in.Type == pb.ChaincodeMessage_KEEPALIVE {
				chaincodeLogger.Debug("Received KEEPALIVE Response")
				// Received a keep alive message, we don't do anything with it for now
				// and it does not touch the state machine
				continue
			}
		case nsInfo = <-handler.nextState:
			in = nsInfo.msg
			if in == nil {
				err = fmt.Errorf("Next state nil message, ending chaincode support stream")
				chaincodeLogger.Debug("Next state nil message, ending chaincode support stream")
				return err
			}
			chaincodeLogger.Debugf("[%s]Move state message %s", shorttxid(in.Txid), in.Type.String())
		case <-handler.waitForKeepaliveTimer():
			if handler.chaincodeSupport.keepalive <= 0 {
				chaincodeLogger.Errorf("Invalid select: keepalive not on (keepalive=%d)", handler.chaincodeSupport.keepalive)
				continue
			}

			//if no error message from serialSend, KEEPALIVE happy, and don't care about error
			//(maybe it'll work later)
			handler.serialSendAsync(&pb.ChaincodeMessage{Type: pb.ChaincodeMessage_KEEPALIVE}, nil)
			continue
		}

		err = handler.HandleMessage(in)
		if err != nil {
			chaincodeLogger.Errorf("[%s]Error handling message, ending stream: %s", shorttxid(in.Txid), err)
			return fmt.Errorf("Error handling message, ending stream: %s", err)
		}

		if nsInfo != nil && nsInfo.sendToCC {
			chaincodeLogger.Debugf("[%s]sending state message %s", shorttxid(in.Txid), in.Type.String())
			//ready messages are sent sync
			if nsInfo.sendSync {
				if in.Type.String() != pb.ChaincodeMessage_READY.String() {
					panic(fmt.Sprintf("[%s]Sync send can only be for READY state %s\n", shorttxid(in.Txid), in.Type.String()))
				}
				if err = handler.serialSend(in); err != nil {
					return fmt.Errorf("[%s]Error sending ready  message, ending stream: %s", shorttxid(in.Txid), err)
				}
			} else {
				//if error bail in select
				handler.serialSendAsync(in, errc)
			}
		}
	}
```

首先是利用 select 结构尝试读取各种消息。包括：

* `case in = <-msgAvail`：从 cc 侧读取到请求消息；
* `case nsInfo = <-handler.nextState`：读取切换到下个状态的附加消息。
* `case <-handler.waitForKeepaliveTimer()`：定期发出心跳刷新消息。

读取到合法消息后，会分别调用 `handler.HandleMessage(in)` 处理 cc 消息；以及检查状态切换消息（仅允许消息类型为 READY，意味着此时 cc 在正常运行状态），是否要发送给 cc 侧（sendToCC 为 True）。


#### FSM 

定义的状态、事件主要在 `func newChaincodeSupportHandler(chaincodeSupport *ChaincodeSupport, peerChatStream ccintf.ChaincodeStream) *Handler ` 方法中。

一般对应 GET_STATE、GET_STATE_BY_RANGE 等简单事件，调用 handleXXX 方法。

PUT_STATE、DEL_STATE、INVOKE_CHAINCODE 三个事件，则会触发 enterBusyState() 方法。