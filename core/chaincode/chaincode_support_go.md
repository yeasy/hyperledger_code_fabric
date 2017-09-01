### chaincode_support.go

主要实现 ChaincodeSupport 结构，这是 peer 侧对链码支持的主要数据结构。peer 启动后，会初始化一个该结构体。

```go
type ChaincodeSupport struct {
	auth              accesscontrol.Authenticator
	runningChaincodes *runningChaincodes
	peerAddress       string
	ccStartupTimeout  time.Duration
	peerNetworkID     string
	peerID            string
	peerTLSCertFile   string
	peerTLSKeyFile    string
	peerTLSSvrHostOrd string
	keepalive         time.Duration
	chaincodeLogLevel string
	shimLogLevel      string
	logFormat         string
	executetimeout    time.Duration
	userRunsCC        bool
	peerTLS           bool
}
```

其中，runningChaincodes 结构十分重要，其中通过 chaincodeMap 字典结构维护所有关联链码容器的处理句柄。每一个启动后的链码容器都会对应到这里面的一个句柄。

```go
// runningChaincodes contains maps of chaincodeIDs to their chaincodeRTEs
type runningChaincodes struct {
	sync.RWMutex
	// chaincode environment for each chaincode
	chaincodeMap map[string]*chaincodeRTEnv

	//mark the starting of launch of a chaincode so multiple requests
	//do not attempt to start the chaincode at the same time
	launchStarted map[string]bool
}

type chaincodeRTEnv struct {
	handler *Handler
}
```

方法主要包括：

* `Execute(ctxt context.Context, cccid *ccprovider.CCContext, msg *pb.ChaincodeMessage, timeout time.Duration) (*pb.ChaincodeMessage, error)`：在链码侧执行一个交易。
* `HandleChaincodeStream(ctxt context.Context, stream ccintf.ChaincodeStream) error`：响应链码容器消息流。
* `Launch(context context.Context, cccid *ccprovider.CCContext, spec interface{}) (*pb.ChaincodeID, *pb.ChaincodeInput, error)`：启动一个链码容器，等待它完成注册。
* `Register(stream pb.ChaincodeSupport_RegisterServer) error`：创建并初始化 peer 端的 chaincode处理的 Handler。
* `Stop(context context.Context, cccid *ccprovider.CCContext, cds *pb.ChaincodeDeploymentSpec) error`：停止一个链码容器。