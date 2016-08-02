### handler.go
consensus 消息的处理器。

主要结构为 ConsensusHandler。

```go
type ConsensusHandler struct {
	peer.MessageHandler
	consenterChan chan *util.Message
	coordinator   peer.MessageHandlerCoordinator
}
```