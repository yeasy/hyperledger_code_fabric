### consumer.go

消费者模块的主要结构和实现。

主要实现了 EventsClient 结构体，供用户使用获取系统中事件。

```go
type EventsClient struct {
	sync.RWMutex
	peerAddress string
	regTimeout  time.Duration
	stream      ehpb.Events_ChatClient
	adapter     EventAdapter
}
```

提供几个主要方法：

* `func (ec *EventsClient) Start() error`：入口方法，该方法会自动创建到 peer 事件流的连接，并开始处理收到的消息。
* `func (ec *EventsClient) Recv() (*ehpb.Event, error)`：从流连接中接收一个事件。
* `func (ec *EventsClient) Stop() error`：终止事件流连接。


启动后主要调用过程如下图所示。

![consumer 客户端启动后流程](../_images/consumer.png)