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

CreateStandardChannelFilters为普通区块链（非系统区块链）创建一组过滤器，包含超时过滤器、消息长度过滤器、签名过滤器。

#### ClassifyMsg

检查消息以确定需要处理的消息类型。

#### ProcessNormalMsg

根据当前配置检查消息是否有效。

#### ProcessConfigUpdateMsg

先调用过滤器校验消息有效性，然后调用channel 的support.ProposeConfigUpdate\(env\)进行消息处理，最后对消息签名，再调用过滤器校验一次。

#### ProcessConfigMsg

执行过程同ProcessConfigUpdateMsg。

