### chaincode_support.go

ChaincodeSupport 结构是 peer 侧对链码支持的主要数据结构。

```go
type ChaincodeSupport struct {
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

包括几个主要方法。

* Execute() 方法：在链码侧执行一个交易。
* HandleChaincodeStream() 方法：响应链码容器消息流。
* Launch() 方法：启动一个链码容器，并完成注册。
* Register()方法：创建并初始化 peer 端的 chaincode处理的 Handler。
* stop()方法：停止一个链码容器。