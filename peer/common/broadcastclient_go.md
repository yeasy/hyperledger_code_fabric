### broadcastclient.go

endorser向orderer发送交易的BroadcastClient接口和功能实现。

```go
type BroadcastClient interface {
	//Send data to orderer
	Send(env *cb.Envelope) error
	Close() error
}

type broadcastClient struct {
	client ab.AtomicBroadcast_BroadcastClient
}
```

#### GetBroadcastClient

创建一个BroadcastClient接口实体，用于跟orderer交互。

* NewOrdererClientFromEnv根据orderer配置文件创建一个跟orderer进行grpc通信的rpc client，然后构建orderclient实例。

* 调用orderclient Broadcast函数返回AtomicBroadcast\_BroadcastClient实体。



#### getAck

broadcastClient调用AtomicBroadcast\_BroadcastClient Recv接口获得应答数据

#### Send

broadcastClient调用AtomicBroadcast\_BroadcastClient Send接口发送数据，再调用Recv接口获得应答数据

#### Close

broadcastClient调用AtomicBroadcast\_BroadcastClient CloseSend接口关闭通信接口

