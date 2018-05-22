#### systemchannelfilter.go

系统channel过滤器实现。

```
// ChainCreator defines the methods necessary to simulate channel creation.
type ChainCreator interface {
	// NewChannelConfig returns a template config for a new channel.
	NewChannelConfig(envConfigUpdate *cb.Envelope) (channelconfig.Resources, error)

	// CreateBundle parses the config into resources
	CreateBundle(channelID string, config *cb.Config) (channelconfig.Resources, error)

	// ChannelsCount returns the count of channels which currently exist.
	ChannelsCount() int
}

// LimitedSupport defines the subset of the channel resources required by the systemchannel filter.
type LimitedSupport interface {
	OrdererConfig() (channelconfig.Orderer, bool)
}

// SystemChainFilter implements the filter.Rule interface.
type SystemChainFilter struct {
	cc      ChainCreator
	support LimitedSupport
}
```

NewSystemChannelFilter创建一个系统channel过滤器。

#### Apply

消息过滤逻辑。

* 解析出消息头里面的ChannelHeader，不为HeaderType\_ORDERER\_TRANSACTION类型，则不做过滤检测
* 获得当前的channel个数和orderer支持的最大channel个数，如果超出则报错
* 从envelope消息体中取出配置信息，调用authorizeAndInspect函数进行channel授权和注入。



