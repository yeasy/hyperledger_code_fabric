#### server.go

orderer提供的服务实现，包含broadcast和deliver接口。

### NewServer

新建一个 Server 结构，包括一个 broadcast 的处理句柄，以及一个 deliver 的处理句柄。

```go
type server struct {
	bh    broadcast.Handler
	dh    deliver.Handler
	debug *localconfig.Debug
	*multichannel.Registrar
}
```

NewServer 分别初始化这两个句柄，挂载上前面初始化的 multichannel.Registrar 结构。broadcast 句柄还需要初始化一个配置更新的处理器，负责处理 CONFIG\_UPDATE 交易。

```go
func NewServer(r *multichannel.Registrar, _ crypto.LocalSigner, debug *localconfig.Debug, timeWindow time.Duration, mutualTLS bool) ab.AtomicBroadcastServer {
	s := &server{
		dh:        deliver.NewHandlerImpl(deliverSupport{Registrar: r}, timeWindow, mutualTLS),
		bh:        broadcast.NewHandlerImpl(broadcastSupport{Registrar: r}),
		debug:     debug,
		Registrar: r,
	}
	return s
}
```



