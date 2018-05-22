### ccproviderimpl.go

ccProviderImpl 结构封装了对链码操作的上层方法。

```go
type ccProviderImpl struct {
	txsim ledger.TxSimulator
}
```


另外，提供 chaincode执行功能。

#### ExecuteChaincode

* 创建简单的 ChaincodeInvocationSpec，只包含链码名字和Args。
* 核心：调用 Execute\(\)方法（见core/chaincode/exectransaction.go），返回响应response和事件event。
* 返回response、event。

