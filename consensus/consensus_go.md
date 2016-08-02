## consensus.go

定义 consensus 相关的抽象接口。

比较重要的一些接口如下，这些接口在各个子包中被继承和实现。

### ExecutionConsumer

```go
type ExecutionConsumer interface {
	Executed(tag interface{})                                // Called whenever Execute completes
	Committed(tag interface{}, target *pb.BlockchainInfo)    // Called whenever Commit completes
	RolledBack(tag interface{})                              // Called whenever a Rollback completes
	StateUpdated(tag interface{}, target *pb.BlockchainInfo) // Called when state transfer completes, if target is nil, this indicates a failure and a new target should be supplied
}
```

### Consenter

consensus plugin 必须继承这个接口，是入口，从网络上获取到有关的消息并开始处理。

```go
type Consenter interface {
	RecvMsg(msg *pb.Message, senderHandle *pb.PeerID) error // Called serially with incoming messages from gRPC
	ExecutionConsumer
}
```

RecvMsg() 方法处理 CHAIN_TRANSACTION 消息 和 CONSENSUS 消息。

```go
func (i *Noops) RecvMsg(msg *pb.Message, senderHandle *pb.PeerID) error {
	if logger.IsEnabledFor(logging.DEBUG) {
		logger.Debugf("Handling Message of type: %s ", msg.Type)
	}
	if msg.Type == pb.Message_CHAIN_TRANSACTION {
		if err := i.broadcastConsensusMsg(msg); nil != err {
			return err
		}
	}
	if msg.Type == pb.Message_CONSENSUS {
		tx, err := i.getTxFromMsg(msg)
		if nil != err {
			return err
		}
		if logger.IsEnabledFor(logging.DEBUG) {
			logger.Debugf("Sending to channel tx uuid: %s", tx.Uuid)
		}
		i.channel <- tx
	}
	return nil
}
```

### Inquirer

辅助获取 VP 网络的相关信息。

```go
type Inquirer interface {
	GetNetworkInfo() (self *pb.PeerEndpoint, network []*pb.PeerEndpoint, err error)
	GetNetworkHandles() (self *pb.PeerID, network []*pb.PeerID, err error)
}
```

### Communicator

辅助发送消息给其它 VP。

```go
type Communicator interface {
	Broadcast(msg *pb.Message, peerType pb.PeerEndpoint_Type) error
	Unicast(msg *pb.Message, receiverHandle *pb.PeerID) error
}
```

### SecurityUtils

包括签名和验证，具体实现时会借助 core/crypto 包。

```go
type SecurityUtils interface {
	Sign(msg []byte) ([]byte, error)
	Verify(peerID *pb.PeerID, signature []byte, message []byte) error
}
```

### Executor

具体执行交易、提交或回滚。会修改账本状态。


```go
type Executor interface {
	Start()                                                                     // Bring up the resources needed to use this interface
	Halt()                                                                      // Tear down the resources needed to use this interface
	Execute(tag interface{}, txs []*pb.Transaction)                             // Executes a set of transactions, this may be called in succession
	Commit(tag interface{}, metadata []byte)                                    // Commits whatever transactions have been executed
	Rollback(tag interface{})                                                   // Rolls back whatever transactions have been executed
	UpdateState(tag interface{}, target *pb.BlockchainInfo, peers []*pb.PeerID) // Attempts to synchronize state to a particular target, implicitly calls rollback if needed
}
```

### LegacyExecutor

Raw Executor接口，目前仍然在被使用，如 executor 中 coordinator 具体处理事件、以及 noops 中的直接调用。将来可能会被完全替换掉。

```go
type LegacyExecutor interface {
	BeginTxBatch(id interface{}) error
	ExecTxs(id interface{}, txs []*pb.Transaction) ([]byte, error)
	CommitTxBatch(id interface{}, metadata []byte) (*pb.Block, error)
	RollbackTxBatch(id interface{}) error
	PreviewCommitTxBatch(id interface{}, metadata []byte) ([]byte, error)
}
```

### ReadOnlyLedger

查询本地账本信息，而不会修改账本。具体实现会借助 core/ledger 包。

```go
type ReadOnlyLedger interface {
	GetBlock(id uint64) (block *pb.Block, err error)
	GetBlockchainSize() uint64
	GetBlockchainInfo() *pb.BlockchainInfo
	GetBlockchainInfoBlob() []byte
	GetBlockHeadMetadata() ([]byte, error)
}
```

### Stack

包括了上述 NetworkStack、SecurityUtils、Executor 接口等一系列句柄方法集合，辅助 consensus 处理。

```go
type Stack interface {
	NetworkStack
	SecurityUtils
	Executor
	LegacyExecutor
	LedgerManager
	ReadOnlyLedger
	StatePersistor
}
```