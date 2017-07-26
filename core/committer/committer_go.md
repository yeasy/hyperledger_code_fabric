### committer.go

定义 Committer 接口。Committer 角色必须实现这一接口。

```go
type Committer interface {

	// Commit block to the ledger
	Commit(block *common.Block) error

	// Get recent block sequence number
	LedgerHeight() (uint64, error)

	// Gets blocks with sequence numbers provided in the slice
	GetBlocks(blockSeqs []uint64) []*common.Block

	// Closes committing service
	Close()
}
```