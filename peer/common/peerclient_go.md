### peerclient.go

PeerClient，描述了跟peer通信的各种功能实现。

#### NewPeerClientFromEnv

根据配置文件配置，创建一个PeerClient。

* 调用configFromEnv，使用配置组件viper获得全局配置中peer各种配置。
* NewGRPCClient根据配置创建一个GRPCClient
* 根据grpc clinet和获得的其它配置信息构建PeerClient返回

#### Endorser

使用peer address创建一个新的NewConnection，然后函数NewEndorserClient使用这个connection构造返回EndorserClient。

#### Admin

使用peer address创建一个新的NewConnection，然后函数NewAdminClient使用这个connection构造返回AdminClient。

#### GetEndorserClient

* 调用NewPeerClientFromEnv获得PeerClient
* 调用Endorser获得EndorserClient

#### GetAdminClient

* 调用NewPeerClientFromEnv获得PeerClient

* 调用Admin获得AdminClient



