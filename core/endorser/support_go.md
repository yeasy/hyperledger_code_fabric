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

* 如果是部署chaincode，调用Execute
* 如果是执行chaincode，先装饰chaincode input，然后执行ExecuteChaincode

#### GetChaincodeDefinition

返回chaincode的resourcesconfig.ChaincodeDefinition结构

#### CheckACL

检查签名proposal是否符合ACL策略

#### IsJavaCC

是否java chaincode

#### CheckInstantiationPolicy

检查账本的实例化策略和chaincode ChaincodeDefinition是否一致

#### GetApplicationConfig

获得channel的SharedConfig配置

#### shorttxid

缩短txid，最多缩短到8位。

