### config.go

跟配置相关的一些方法。

主要是配置的一些选项可能需要一些计算和检测，通过这些方法可以做一些 cache 等。

包括下面一些配置项：

```go
var localAddress string
var localAddressError error
var peerEndpoint *pb.PeerEndpoint
var peerEndpointError error

// Cached values of commonly used configuration constants.
var syncStateSnapshotChannelSize int
var syncStateDeltasChannelSize int
var syncBlocksChannelSize int
var validatorEnabled bool
```

#### CacheConfiguration

定义getLocalAddress、getPeerEndpoint 2个内部函数，然后调用它们得到配置的peer ip address以及peerEndpoint。

#### GetServerConfig

构造并返回peer grpc服务端的TLS配置。

#### GetClientCertificate

返回用户grpc客户端的TLS证书配置。

