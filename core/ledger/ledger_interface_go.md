### ledger_interface.go

定义账本结构相关的接口。

主要包括：

* HistoryQueryExecutor：负责执行历史查询，跟历史数据库打交道。
* PeerLedger：存有所有的交易的账本结构，存在于 peer 侧。跟 orderer 侧账本的区别在于带有交易合法状态的标记。
* PeerLedgerProvider：对 ledger 实例的句柄。
* QueryExecutor：负责执行对账本的查询类操作。
* TxSimulator：endorse 阶段，模拟在当前最新的世界状态上执行交易。
* ValidatedLedger：仅存在 committing 阶段，通过验证的合法交易。