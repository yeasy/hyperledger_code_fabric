### committer.go

定义 Committer 接口。Committer 角色必须实现这一接口。

```go
type Committer interface {

	// Commit block to the ledger
	Commit(block *common.Block) error

	// CommitWithPvtData block and private data into the ledger
	CommitWithPvtData(blockAndPvtData *ledger.BlockAndPvtData) error

	// GetPvtDataAndBlockByNum retrieves block with private data with given
	// sequence number
	GetPvtDataAndBlockByNum(seqNum uint64) (*ledger.BlockAndPvtData, error)

	// Get recent block sequence number
	LedgerHeight() (uint64, error)

	// Gets blocks with sequence numbers provided in the slice
	GetBlocks(blockSeqs []uint64) []*common.Block

	// Closes committing service
	Close()
}
```

其中，最重要的是 `Commit(block *common.Block) error` 方法，负责完成对某个区块的验证的最终确认提交。 