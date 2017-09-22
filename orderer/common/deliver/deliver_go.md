#### deliver.go

对 orderer 服务的 Deliver 请求（如获取某个区块），最终会调用到这里的 deliverServer 结构的 `Handle(srv ab.AtomicBroadcast_DeliverServer) error` 方法。

```go
type deliverServer struct {
	sm SupportManager
}

func (ds *deliverServer) Handle(srv ab.AtomicBroadcast_DeliverServer) error
```

Handle 方法会开启一个循环来处理请求消息，对每一个消息，调用 `deliverBlocks(srv ab.AtomicBroadcast_DeliverServer, envelope *cb.Envelope) error` 方法进行处理。


##### 获取通道头

```go
payload, err := utils.UnmarshalPayload(envelope.Payload)
chdr, err := utils.UnmarshalChannelHeader(payload.Header.ChannelHeader)
```

##### 获取对应的链结构

```go
chain, ok := ds.sm.GetChain(chdr.ChannelId)
```


##### 检查权限

检查请求方是否具备 ChannelReaders 角色的权限。

```go
sf := msgprocessor.NewSigFilter(policies.ChannelReaders, chain.PolicyManager())
if err := sf.Apply(envelope); err != nil {
	logger.Warningf("[channel: %s] Received unauthorized deliver request from %s: %s", chdr.ChannelId, addr, err)
	return sendStatusReply(srv, cb.Status_FORBIDDEN)
}
```


##### 查找区块

将请求信封结构的 Payload.Data 域，转换为 `ab.SeekInfo` 类型的结构。

根据 `ab.SeekInfo` 结构中指定的 Start、Stop 范围循环发送出区块答复消息给请求方。

```go
block, status := cursor.Next()
sendBlockReply(srv, block)
```
