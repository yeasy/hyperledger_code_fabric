### executor.go

#### coordinatorImpl 结构体

coordinatorImpl 结构比较重要，负责完成相关处理。

```go
type coordinatorImpl struct {
	manager         events.Manager              // Maintains event thread and sends events to the coordinator
	rawExecutor     PartialStack                // Does the real interaction with the ledger
	consumer        consensus.ExecutionConsumer // The consumer of this coordinator which receives the callbacks
	stc             statetransfer.Coordinator   // State transfer instance
	batchInProgress bool                        // Are we mid execution batch
	skipInProgress  bool                        // Are we mid state transfer
}
```

继承自 consensus.Executor 接口，以事件队列的方式实现了相关方法（各方法对应的事件也在该文件中定义）。

* ProcessEvent()：收到事件后进行处理，包括 executeEvent、commitEvent、rollbackEvent、stateUpdateEvent。
* Commit()：添加一个 commit 事件到队列。
* Execute()：添加一个 execute 事件到队列。
* Rollback()：添加一个 rollback 事件到队列。
* UpdateState()：添加一个 updatestate 事件到队列。
* Start()：启动 peer 的状态跳转和事件的管理器。
* Halt()：停止 peer 的状态跳转和事件的管理器。

#### NewImpl() 方法

对外的 NewImpl() 方法返回一个创建的 Executor 接口，包内即为 coordinatorImpl 结构。

```go
func NewImpl(consumer consensus.ExecutionConsumer, rawExecutor PartialStack, stps statetransfer.PartialStack) consensus.Executor {
	co := &coordinatorImpl{
		rawExecutor: rawExecutor,
		consumer:    consumer,
		stc:         statetransfer.NewCoordinatorImpl(stps),
		manager:     events.NewManagerImpl(),
	}
	co.manager.SetReceiver(co)
	return co
}
```