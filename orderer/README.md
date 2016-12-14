# orderer

orderer 包负责对推荐来的合法 transaction 进行全局排序并广播。

orderer 处理后的 transactions 拥有全局唯一的顺序，并且每个通过区块链结构保持前一个 transaction 的信息。

协议定义在 [protos/orderer/ab.proto](protos/orderer/ab.proto)中，主要包括两个服务：

* Broadcast：peer 节点将带有 transaction 信息的消息块（blob）通过该服务发送到某个 channel。
* Deliver：consensus 节点通过该接口将排序好的消息组返回到 peer 节点。


目前实现上是可插拔结构，支持三种排序服务：

* solo：单节点，提供简单低性能的排序，需要后端 raw ledger 支持，供测试用。
* Kafka：利用 Kafka 队列系统进行排序，不支持 byzantine 容错，无需 raw ledger 支持，可扩展。
* PBFT：支持 byzantine 容错，需要后端 raw ledger 支持。