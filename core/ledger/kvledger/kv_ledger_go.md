#### kv_ledger.go

实现一个支持键值对的 peer 账本结构体。

```go
type kvLedger struct {
	ledgerID   string
	blockStore blkstorage.BlockStore
	txtmgmt    txmgr.TxMgr
	historyDB  historydb.HistoryDB
}
```