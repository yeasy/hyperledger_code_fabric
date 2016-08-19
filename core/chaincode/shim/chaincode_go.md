#### chaincode.go

定义接口 Chaincode 和 ChaincodeStub。

```go
type Chaincode interface { 
// Init is called during Deploy transaction after the container has been 
// established, allowing the chaincode to initialize its internal data 
Init(stub *ChaincodeStub, function string, args []string) ([]byte, error) 

// Invoke is called for every Invoke transactions. The chaincode may change 
// its state variables 
Invoke(stub *ChaincodeStub, function string, args []string) ([]byte, error) 

// Query is called for Query transactions. The chaincode may only read 
// (but not modify) its state variables and return the result 
Query(stub *ChaincodeStub, function string, args []string) ([]byte, error)}
```
用户自己编写的 chaincode 代码需要实现这三个接口，在代码中通过 stub 来跟 ledger 交互。

```go
type ChaincodeStub struct { 
UUID string 
securityContext *pb.ChaincodeSecurityContext 
chaincodeEvent *pb.ChaincodeEvent 
args [][]byte
}
```
