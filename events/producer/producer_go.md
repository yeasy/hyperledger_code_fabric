### producer.go


#### NewEventsServer() 方法

该方法会创建一个全局的事件服务器（EventsServer），并完成初始化工作。

```go
// NewEventsServer returns a EventsServer
func NewEventsServer(bufferSize uint, timeout time.Duration) *EventsServer {
	if globalEventsServer != nil {
		panic("Cannot create multiple event hub servers")
	}
	globalEventsServer = new(EventsServer)
	initializeEvents(bufferSize, timeout)
	//initializeCCEventProcessor(bufferSize, timeout)
	return globalEventsServer
}
```

代码中会初始化两个全局变量：

* globalEventsServer：全局唯一的事件服务器。
* gEventProcessor：全局唯一的 [事件处理器](events_go.md)。

initializeEvents(bufferSize, timeout)方法会注册内部消息类型（包括 EventType_BLOCK、EventType_CHAINCODE、EventType_REJECTION 和 EventType_REGISTER），并启动 gEventProcessor。

```go
func initializeEvents(bufferSize uint, tout time.Duration) {
	if gEventProcessor != nil {
		panic("should not be called twice")
	}

	gEventProcessor = &eventProcessor{eventConsumers: make(map[pb.EventType]handlerList), eventChannel: make(chan *pb.Event, bufferSize), timeout: tout}

	addInternalEventTypes()

	//start the event processor
	go gEventProcessor.start()
}
```

gEventProcessor 启动后会不断从 eventChannel 通道中读取消息，并调用绑定的 handler 的 SendMessage() 方法发送出去。

#### EventsServer 结构体

主要实现了事件服务器结构体。

```go
// EventsServer implementation of the Peer service
type EventsServer struct {
}
```

结构体实现了如下方法：

* `func (p *EventsServer) Chat(stream pb.Events_ChatServer) error`：调用该方法会发送消息到该事件服务器。事件服务器会生成一个新的事件处理句柄，并调用句柄的 HandleMessage() 方法对消息进行处理。