### committer_impl.go

LedgerCommitter 结构体实现了 Committer 接口。

```go
type LedgerCommitter struct {
	ledger    ledger.PeerLedger
	validator txvalidator.Validator
	eventer   ConfigBlockEventer
}
```

