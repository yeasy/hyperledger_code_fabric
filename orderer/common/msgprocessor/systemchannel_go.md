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



