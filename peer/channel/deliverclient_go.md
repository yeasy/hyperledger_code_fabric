### deliverclient.go

与orderer交互的deliver rpc接口相关实现。

#### newDeliverClient

创建一个deliverclient。

先调用peer/common/orderclient.go NewOrdererClientFromEnv，根据orderer配置文件创建grpc client。

