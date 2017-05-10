#### chaincode.go


```go
type ChaincodeStub struct { 
UUID string 
securityContext *pb.ChaincodeSecurityContext 
chaincodeEvent *pb.ChaincodeEvent 
args [][]byte
}
```
