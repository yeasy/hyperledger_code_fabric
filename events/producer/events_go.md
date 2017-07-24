### events.go

事件产生的入口。

主要实现了 eventProcessor 结构体，负责实现对事件的处理。

```go
type eventProcessor struct {
	sync.RWMutex
	eventConsumers map[pb.EventType]handlerList

	//we could generalize this with mutiple channels each with its own size
	eventChannel chan *pb.Event

	//timeout duration for producer to send an event.
	//if < 0, if buffer full, unblocks immediately and not send
	//if 0, if buffer full, will block and guarantee the event will be sent out
	//if > 0, if buffer full, blocks till timeout
	timeout time.Duration
}
```

start() 方法启动后会进入主循环，不断检测 eventChannel 中是否有事件（由组件产生），如果有，则调用对应的 handler 处理。