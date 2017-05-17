## configupdate

处理配置更新，主要包括 Processor 结构。

```golang
type Processor struct {
	signer          crypto.LocalSigner
	manager         SupportManager
	systemChannelID string
}
```

提供 Process 方法处理配置更新的消息，转换为对应的请求，如创建 channel 或者更新 channel。