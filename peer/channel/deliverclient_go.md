### deliverclient.go

与orderer交互的deliver rpc接口相关实现。

#### newDeliverClient

创建一个deliverclient。

先调用peer/common/orderclient.go NewOrdererClientFromEnv，根据orderer配置文件创建grpc OrdererClient。

再调用OrdererClient Deliver函数创建AtomicBroadcast\_DeliverClient。

最后计算证书的sha256 hash值保存，构造deliverclient结构返回。

