#### blockcutter.go

区块切割功能，收到消息后根据消息长度进行切割。通过实现Receiver接口来提供功能。

```go
type Receiver interface {
    Ordered(msg *cb.Envelope) (messageBatches [][]*cb.Envelope, pending bool)

    Cut() []*cb.Envelope
}
```

#### NewReceiverImpl

新建一个Receiver实例，其实就是block cutter实例。

#### Ordered

按收到的消息顺序调用此函数。根据消息长度与r.sharedConfigManager.BatchSize\(\).PreferredMaxBytes比较，如果超过能支持的最大长度，则进行切割。或者等待处理消息加上此消息长度大于支持最大消息成都r.pendingBatchSizeBytes+messageSizeBytes&gt;r.sharedConfigManager.BatchSize\(\).PreferredMaxBytes也要进行切割。最后返回切割后的多个区块消息。

#### Cut

切割函数，将区块消息进行切割。

