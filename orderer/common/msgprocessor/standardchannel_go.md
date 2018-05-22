#### standardchannel.go

标准通道消息处理器实现。

```
// StandardChannelSupport includes the resources needed for the StandardChannel processor.
type StandardChannelSupport interface {
	// Sequence should return the current configSeq
	Sequence() uint64

	// ChainID returns the ChannelID
	ChainID() string

	// Signer returns the signer for this orderer
	Signer() crypto.LocalSigner

	// ProposeConfigUpdate takes in an Envelope of type CONFIG_UPDATE and produces a
	// ConfigEnvelope to be used as the Envelope Payload Data of a CONFIG message
	ProposeConfigUpdate(configtx *cb.Envelope) (*cb.ConfigEnvelope, error)
}

// StandardChannel implements the Processor interface for standard extant channels
type StandardChannel struct {
	support StandardChannelSupport
	filters *RuleSet
}
```

NewStandardChannel创建一个标准消息处理器实例。

CreateStandardChannelFilters为普通区块链（非系统区块链）创建一组过滤器，包含超时过滤器、

