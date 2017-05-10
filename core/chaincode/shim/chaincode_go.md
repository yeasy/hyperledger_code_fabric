#### chaincode.go

提供 ChaincodeStub 结构，支持一系列对账本进行操作的方法（如 GetState、PutState、DelState 等），这些方法用户可以直接在链码中进行调用。

```go
type ChaincodeStub struct {
	TxID           string
	chaincodeEvent *pb.ChaincodeEvent
	args           [][]byte
	handler        *Handler
	signedProposal *pb.SignedProposal
	proposal       *pb.Proposal

	// Additional fields extracted from the signedProposal
	creator   []byte
	transient map[string][]byte
	binding   []byte
}
```

stub 会进一步调用本地的 Handler 结构提供的方法，主要过程都是封装为 ChaincodeMessage，发给 peer 节点进行指定的操作。
