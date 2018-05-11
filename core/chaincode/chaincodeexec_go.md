### chaincodeexec.go

chaincode执行功能。

#### ExecuteChaincode

* 创建简单的 ChaincodeInvocationSpec，只包含链码名字和Args。
* 核心：调用 Execute\(\)方法（见core/chaincode/exectransaction.go），返回响应response和事件event。
* 返回response、event。



