### committer.go

定义 Committer 接口。Committer 角色必须实现这一接口。

```go
type Committer interface {

    // CommitWithPvtData block and private data into the ledger
    CommitWithPvtData(blockAndPvtData *ledger.BlockAndPvtData) error

    // GetPvtDataAndBlockByNum retrieves block with private data with given
    // sequence number
    GetPvtDataAndBlockByNum(seqNum uint64) (*ledger.BlockAndPvtData, error)
    
    // GetPvtDataByNum returns a slice of the private data from the ledger
    // for given block and based on the filter which indicates a map of
    // collections and namespaces of private data to retrieve
    GetPvtDataByNum(blockNum uint64, filter ledger.PvtNsCollFilter) ([]*ledger.TxPvtData, error)

    // Get recent block sequence number
    LedgerHeight() (uint64, error)

    // Gets blocks with sequence numbers provided in the slice
    GetBlocks(blockSeqs []uint64) []*common.Block

    // Closes committing service
    Close()
}
```



