### adapter.go

定义一个事件的 Adapter 接口，获取感兴趣的事件类型，接收事件。

```go
//EventAdapter is the interface by which a fabric event client registers interested events and
//receives messages from the fabric event Server
type EventAdapter interface {
	GetInterestedEvents() ([]*peer.Interest, error)
	Recv(msg *peer.Event) (bool, error)
	Disconnected(err error)
}
```