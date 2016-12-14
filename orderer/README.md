# orderer

orderer 包负责对推荐来的合法 transaction 进行全局排序并广播。

orderer 处理后的 transactions 拥有全局唯一的顺序，并且每个通过区块链结构保持前一个 transaction 的信息。