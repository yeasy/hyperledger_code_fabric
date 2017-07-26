### committer_impl.go

LedgerCommitter 结构体实现了 Committer 接口。

```go
type LedgerCommitter struct {
	ledger    ledger.PeerLedger
	validator txvalidator.Validator
	eventer   ConfigBlockEventer
}
```

其中，最重要的是 `Commit(block *common.Block) error` 方法，负责完成对某个区块的验证的最终确认提交。 主要流程如下图所示。

![](../_images/core_committer_LedgerCommitter_Commit.png)