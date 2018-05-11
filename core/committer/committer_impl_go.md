### committer\_impl.go

LedgerCommitter 结构体实现了 Committer 接口。

```go
type LedgerCommitter struct {
	ledger.PeerLedger
	eventer ConfigBlockEventer
}
```

实现的几个方法，都是通过调用成员 ledger 的方法来实现。

其中，最重要的提交函数在1.0版本是 `Commit(block *common.Block) error` 方法，1.1版本拆分成了preCommit、postCommit2个过程，负责完成对某个区块的验证的最终确认提交。

#### NewLedgerCommitter

#### NewLedgerCommitterReactive

创建committer实例的工厂函数，在peer初始化每个channal账本时调用此函数，见peer.go。

#### CommitWithPvtData

提交区块的函数，处理流程如下：

* preCommit，在提交区块前验证区块有效性，并更新区块配置

* lc.PeerLedger.CommitWithPvtData，使用账本的提交函数进行区块提交

* postCommit，区块提交到账本后，创建并广播区块事件。



