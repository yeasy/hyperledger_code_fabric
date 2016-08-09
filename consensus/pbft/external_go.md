### external.go
提供可以从 pbft 包之外被调用的各种结构和接口。

例如 externalEventReceiver 结构，实现了一个 Consenter 接口。

```go
type externalEventReceiver struct {
	manager events.Manager
}
```

方法实现上，主要是把相关事件放入到队列中。

```go
// RecvMsg is called by the stack when a new message is received
func (eer *externalEventReceiver) RecvMsg(ocMsg *pb.Message, senderHandle *pb.PeerID) error {
	eer.manager.Queue() <- batchMessageEvent{
		msg:    ocMsg,
		sender: senderHandle,
	}
	return nil
}

// Executed is called whenever Execute completes, no-op for noops as it uses the legacy synchronous api
func (eer *externalEventReceiver) Executed(tag interface{}) {
	eer.manager.Queue() <- executedEvent{tag}
}

// Committed is called whenever Commit completes, no-op for noops as it uses the legacy synchronous api
func (eer *externalEventReceiver) Committed(tag interface{}, target *pb.BlockchainInfo) {
	eer.manager.Queue() <- committedEvent{tag, target}
}

// RolledBack is called whenever a Rollback completes, no-op for noops as it uses the legacy synchronous api
func (eer *externalEventReceiver) RolledBack(tag interface{}) {
	eer.manager.Queue() <- rolledBackEvent{}
}

// StateUpdated is a signal from the stack that it has fast-forwarded its state
func (eer *externalEventReceiver) StateUpdated(tag interface{}, target *pb.BlockchainInfo) {
	eer.manager.Queue() <- stateUpdatedEvent{
		chkpt:  tag.(*checkpointMessage),
		target: target,
	}
}
```