### ordererclient.go

OrdererClient，描述了跟orderer通信的各种功能实现。

#### NewOrdererClientFromEnv

根据配置文件配置，创建一个OrdererClient。

* 调用configFromEnv，使用配置组件viper获得全局配置中orderer各种配置。
* NewGRPCClient根据配置创建一个GRPCClient
* 根据grpc clinet和获得的其它配置信息构建OrdererClient返回

#### Broadcast

使用orderer address创建一个新的NewConnection，然后使用这个connection构造grpc NewClientStream，返回AtomicBroadcast\_BroadcastClient。

#### Deliver

使用orderer address创建一个新的NewConnection，然后使用这个connection构造grpc NewClientStream，返回AtomicBroadcast\_DeliverClient。

#### Certificate

如果服务端需要进行认证，返回tls认证信息

