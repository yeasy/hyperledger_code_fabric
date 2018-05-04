### support.go

实现endorser.Support接口，endorser常用功能。

#### IsSysCCAndNotInvokableExternal

返回chaincode是否系统chaincode并且不能外部调用。

#### GetTxSimulator

返回peer账本的交易模拟器。一个client有多个交易模拟器，是用txid进行区分的。

#### GetHistoryQueryExecutor

返回peer账本的HistoryQueryExecutor

#### GetTransactionByID

根据txid获得交易信息

#### IsSysCC

是否是系统chaincode

#### Execute

执行提案proposal

