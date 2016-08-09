### batch.go
主要定义 Consenter 的一个实现 pbft-batch。

数据结构为 

```go
type obcBatch struct {
	obcGeneric
	externalEventReceiver     // consenter 实现
	pbft        *pbftCore     // pbft 核心引擎
	broadcaster *broadcaster

	batchSize        int
	batchStore       []*Request
	batchTimer       events.Timer
	batchTimerActive bool
	batchTimeout     time.Duration

	manager events.Manager // TODO, remove eventually, the event manager

	incomingChan chan *batchMessage // Queues messages for processing by main thread
	idleChan     chan struct{}      // Idle channel, to be removed

	reqStore *requestStore // Holds the outstanding and pending requests

	deduplicator *deduplicator

	persistForward
}
```

提供完整的 consensus 功能接口，主要包括：

* newObcBatch() 会初始化一个 obcBatch 对象，其中配置事件接受者为 obcBatch 对象；