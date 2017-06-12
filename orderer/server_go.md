## server.go

提供三个重要的数据结构

### deliverSupport

负责处理 deliver 消息。

```go
type deliverSupport struct {
	multichain.Manager
}
```

### broadcastSupport

负责处理 broadcast 消息。

```go
type broadcastSupport struct {
	multichain.Manager
	broadcast.ConfigUpdateProcessor
}
```

### configUpdateSupport

负责处理配置更新交易。

```go
type configUpdateSupport struct {
	multichain.Manager
}
```