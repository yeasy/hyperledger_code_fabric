### ledger.go
十分核心的账本模块。包括维护区块链和世界观两个主要功能。

Ledger 数据结构：

```go
type Ledger struct {
	blockchain *blockchain
	state      *state.State
	currentID  interface{}
}
```

提供三类方法：

* 交易相关：主要被 consenus 模块调用，BeginTxBatch、CommitTxBatch 和 RollbackTxBatch。
* 世界观相关：主要被 chaincode 请求来调用，查看和修改世界观数据库。
* 区块链相关：获取区块链和交易信息等。