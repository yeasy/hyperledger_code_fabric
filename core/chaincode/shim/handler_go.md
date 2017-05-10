#### handler.go

最主要的是实现了 Handler 结构体，具体实现来自 chaincode 的各种对账本的操作。

```go
type Handler struct {
	sync.RWMutex
	//shim to peer grpc serializer. User only in serialSend
	serialLock sync.Mutex
	To         string
	ChatStream PeerChaincodeStream
	FSM        *fsm.FSM
	cc         Chaincode
	// Multiple queries (and one transaction) with different txids can be executing in parallel for this chaincode
	// responseChannel is the channel on which responses are communicated by the shim to the chaincodeStub.
	responseChannel map[string]chan pb.ChaincodeMessage
	nextState       chan *nextStateInfo
}
```

成员主要包括：

* ChatStream：跟 Peer 进行通信的 grpc 流。
* FSM：最重要的事件处理状态机，根据收到不同事件调用不同方法。
* cc：所面向的链码。
* responseChannel：本地 chan，key 是 TxID，value 里面可以放上一些消息，供调用者后面使用。
* nextState：

##### FSM


从 Payload 中解析出 ChaincodeInput 结构，利用这些信息，调用 stub.init 方法对 stub 进行初始化（配置 TxID、args、handler、signedProposal、creator、transient、binding 等成员）。之后，调用 Handler 结构成员 chaincode 