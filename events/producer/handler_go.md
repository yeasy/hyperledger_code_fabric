### handler.go

handler 是针对某种消息的处理句柄。

```go
type handler struct {
	ChatStream       pb.Events_ChatServer
	interestedEvents map[string]*pb.Interest
}
```