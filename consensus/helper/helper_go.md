### helper.go
主要结构为 

```go
type Helper struct {
	consenter    consensus.Consenter
	coordinator  peer.MessageHandlerCoordinator
	secOn        bool
	valid        bool // Whether we believe the state is up to date
	secHelper    crypto.Peer
	curBatch     []*pb.Transaction       // TODO, remove after issue 579
	curBatchErrs []*pb.TransactionResult // TODO, remove after issue 579
	persist.Helper

	executor consensus.Executor
}
```

提供了大量的实际处理函数，包括交易的验证、处理、消息的广播、区块信息查找等。