#### interfaces.go
定义接口 Chaincode 和 ChaincodeStubInterface。

```go
type Chaincode interface {
	// Init is called during Deploy transaction after the container has been
	// established, allowing the chaincode to initialize its internal data
	Init(stub ChaincodeStubInterface) pb.Response
	// Invoke is called for every Invoke transactions. The chaincode may change
	// its state variables
	Invoke(stub ChaincodeStubInterface) pb.Response
}
```
用户自己编写的 chaincode 代码需要实现这两个方法，在代码中通过 stub 来跟 ledger 进行交互。

