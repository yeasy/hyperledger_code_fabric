#### systemchannel.go

系统通道消息处理器实现。

```
// ChannelConfigTemplator can be used to generate config templates.
type ChannelConfigTemplator interface {
    // NewChannelConfig creates a new template configuration manager.
    NewChannelConfig(env *cb.Envelope) (channelconfig.Resources, error)
}

// SystemChannel implements the Processor interface for the system channel.
type SystemChannel struct {
    *StandardChannel
    templator ChannelConfigTemplator
}
```

NewSystemChannel创建一个标准消息处理器实例。

CreateSystemChannelFilters为普通区块链（非系统区块链）创建一组过滤器，包含超时过滤器、消息长度过滤器、签名过滤器、系统通道过滤器。

#### ProcessNormalMsg

* 判断消息的channelid和系统channelid是否一致，不一致就报错。这里需要说明：对于标准通道消息处理，我们不检查channelID，因为消息处理器是由channelID查找的。然而，系统通道消息处理器是捕获全部消息。它不对应于现存的通道，所以我们必须在这里检查它。
* 调用标准通道消息处理函数进行处理s.StandardChannel.ProcessNormalMsg\(msg\)

#### ProcessConfigUpdateMsg

* 判断消息的channelid和系统channelid是否一致，一致就调用标准通道配置消息处理函数s.StandardChannel.ProcessConfigUpdateMsg\(envConfigUpdate\)。
* 消息的channelid和系统channelid不一致，这一定是一个创建通道的交易。新建通道配置信息，验证这个新的配置交易，将新通道配置信息进行签名，将此签名后的消息在系统channel上构造成HeaderType\_ORDERER\_TRANSACTION消息，再次签名后返回。

#### ProcessConfigMsg

* 解析Envelope消息头得到消息类型
* HeaderType\_CONFIG类型，系统通道本身是配置的目标，解析出ConfigUpdate信息，调用底层标准通道函数s.StandardChannel.ProcessConfigUpdateMsg\(configEnvelope.LastUpdate\)处理。

* HeaderType\_ORDERER\_TRANSACTION类型，是一个创建通道的消息，解析出ConfigUpdate信息，调用s.ProcessConfigUpdateMsg\(configEnvelope.LastUpdate\)处理。



