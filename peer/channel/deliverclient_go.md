### deliverclient.go

与orderer交互的deliver rpc接口相关实现，包含创建deliver client和各种查询block接口。

#### newDeliverClient

创建一个deliverclient。

先调用peer/common/orderclient.go NewOrdererClientFromEnv，根据orderer配置文件创建grpc OrdererClient。

再调用OrdererClient Deliver函数创建AtomicBroadcast\_DeliverClient。

最后计算证书的sha256 hash值保存，构造deliverclient结构返回。

#### seekHelper

查找block的接口函数，先构造查询信息seekinfo，然后调用CreateSignedEnvelopeWithTLSBinding构造查询envelope结构。

#### seekSpecified

查找指定区块，根据block num参数构造参数并调用seekhelper返回查找envelope，然后调用deliverclient发送查找信息给orderer。

#### seekOldest

查找最老的区块，构造olderst参数并调用seekhelper返回查找envelope，然后调用deliverclient发送查找信息给orderer。

#### seekNewest

查找最新的区块，构造newest参数并调用seekhelper返回查找envelope，然后调用deliverclient发送查找信息给orderer。

#### readBlock

读取区块信息。调用deliver client recv函数从orderer获取区块信息。

