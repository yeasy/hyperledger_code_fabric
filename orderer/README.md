# orderer
在 fabric 1.0 架构中，共识功能被抽取出来，作为单独的 fabric-orderer 模块来实现，完成核心的排序功能。最核心的功能是实现从客户端过来的 broadcast 请求，和从 orderer 发送到 peer 节点的 deliver 接口。同时，orderer 需要支持多 channel 的维护。

目前，orderer 模块支持三种排序类型：

* Solo：单节点的排序功能，试验性质，不具备可扩展性和容错；
* Kafka：基于 Kafka 集群的排序实现，支持可持久化和可扩展性；
* BFT：支持 BFT 容错的排序实现，尚未完成。

账本记录上支持两种类型：

* Ram：存放近期若干（默认为 1000 个）区块到内存中。
* File：存放区块记录到文件系统，默认是临时目录下的 `hyperledger-fabric-ordererledger` 文件。

[sampleconfig](../sampleconfig/README.md) 目录下有示例的配置文件 [orderer.yaml](../sampleconfig/orderer_yaml.md)。