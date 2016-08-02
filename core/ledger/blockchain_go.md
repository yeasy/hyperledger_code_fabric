### blockchain.go

包括区块链对象，以及查询区块信息、插入新区块等一系列方法。

blockchain 数据结构：

```go
type blockchain struct {
	size               uint64
	previousBlockHash  []byte
	indexer            blockchainIndexer
	lastProcessedBlock *lastProcessedBlock
}
```

* 查询类方法：getLastBlock、getSize、getBlock、getBlockByHash、getTransactionByUUID、getTransactions、getTransactionsByBlockHash、getTransaction、getTransactionByBlockHash、getBlockchainInfo、getBlockchainInfoForBlock。
* 更新类方法：buildBlock 和 addPersistenceChangesForNewBlock，主要在commit交易时被调用。