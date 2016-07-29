#### ccintf.go

比较重要的是

```go
//CCID encapsulates chaincode ID
type CCID struct {
	ChaincodeSpec *pb.ChaincodeSpec
	NetworkID     string
	PeerID        string
}
```