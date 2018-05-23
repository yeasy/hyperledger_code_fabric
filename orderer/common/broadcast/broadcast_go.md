#### broadcast.go

对 orderer 服务的 Broadcast 请求，会交给 `orderer.common.server` 包中 server 结构体的 `Broadcast(srv ab.AtomicBroadcast_BroadcastServer) error` 方法处理。该方法主要会调用到 `orderer.common.broadcast` 包中的 handlerImpl 结构的 `func (bh *handlerImpl) Handle(srv ab.AtomicBroadcast_BroadcastServer) error` 方法。

handlerImpl 结构体十分重要，完成对 Broadcast 请求的核心处理过程。

```go
type handlerImpl struct {
    sm ChannelSupportRegistrar
}

func (bh *handlerImpl) Handle(srv ab.AtomicBroadcast_BroadcastServer) error
```

`Handle(srv ab.AtomicBroadcast_BroadcastServer) error` 方法会开启一个循环来从 srv 中读取请求消息并进行处理，直到结束。

##### 解析消息

首先，解析消息，获取消息头、是否为配置消息、获取对应处理器结构。

```go
chdr, isConfig, processor, err := bh.sm.BroadcastChannelSupport(msg)
```

实际上，映射到 orderer.common.server 包中 broadcastSupport 结构体的 `BroadcastChannelSupport(msg *cb.Envelope) (*cb.ChannelHeader, bool, broadcast.ChannelSupport, error)` 方法，进一步调用到 orderer.common.multichannel 包中 Registrar 结构体的对应方法。

```go
func (r *Registrar) BroadcastChannelSupport(msg *cb.Envelope) (*cb.ChannelHeader, bool, *ChainSupport, error) {
    chdr, err := utils.ChannelHeader(msg)
    if err != nil {
        return nil, false, nil, fmt.Errorf("could not determine channel ID: %s", err)
    }

    cs, ok := r.chains[chdr.ChannelId] //应用通道、系统通道
    if !ok {
        cs = r.systemChannel
    }

    isConfig := false
    switch cs.ClassifyMsg(chdr) {
    case msgprocessor.ConfigUpdateMsg:
        isConfig = true
    default:
    }

    return chdr, isConfig, cs, nil
}
```

channel 头部从消息信封结构中解析出来；是否为配置信息根据消息头中类型进行判断（是否为 cb.HeaderType\_CONFIG\_UPDATE）;通过字典查到对应的 ChainSupport 结构（应用通道、系统通道）作为处理器。

之后，利用解析后的结果，分别对不同类型的消息（普通消息、配置消息）进行不同处理。下面以应用通道的 ChainSupport 结构作为处理器进行介绍。

##### 处理非配置消息

对于非配置消息，主要执行如下两个操作：消息检查和入队列操作。

```go
configSeq, err := processor.ProcessNormalMsg(msg) //消息检查
processor.Order(msg, configSeq) //入队列操作
```

消息检查方法会映射到 orderer.common.msgprocessor 包中 StandardChannel 结构体的 `ProcessNormalMsg(env *cb.Envelope) (configSeq uint64, err error)` 方法，实现如下。

```go
func (s *StandardChannel) ProcessNormalMsg(env *cb.Envelope) (configSeq uint64, err error) {
    configSeq = s.support.Sequence() // 获取配置的序列号，映射到 common.configtx 包中 configManager 结构体的对应方法
    err = s.filters.Apply(env) // 进行过滤检查，实现为 orderer.common.msgprocessor 包中 RuleSet 结构体的对应方法。
    return
}
```

其中，过滤器会在创建 ChainSupport 结构时候初始化：

* 应用通道：orderer.common.mspprocessor 包中的 `CreateStandardChannelFilters(filterSupport channelconfig.Resources) *RuleSet` 方法，包括 EmptyRejectRule、SizeFilter 和 SigFilter（ChannelWriters 角色）。
* 系统通道：orderer.common.mspprocessor 包中的 `CreateSystemChannelFilters(chainCreator ChainCreator, ledgerResources channelconfig.Resources) *RuleSet` 方法，包括 EmptyRejectRule、SizeFilter、SigFilter（ChannelWriters 角色）和 SystemChannelFilter。

入队列操作会根据 consensus 配置的不同映射到 orderer.consensus.solo 包或 orderer.consensus.kafka 包中的方法。

以 kafka 情况为例，会映射到 chainImpl 结构体的对应方法。该方法会将消息进一步封装为 `sarama.ProducerMessage` 类型消息，通过 enqueue 方法发给 Kafka 后端。

```go
func (chain *chainImpl) Order(env *cb.Envelope, configSeq uint64) error {
    marshaledEnv, err := utils.Marshal(env)
    if err != nil {
        return fmt.Errorf("cannot enqueue, unable to marshal envelope because = %s", err)
    }
    if !chain.enqueue(newNormalMessage(marshaledEnv, configSeq)) {
        return fmt.Errorf("cannot enqueue")
    }
    return nil
}
```

##### 处理配置消息

对于配置消息，处理过程与正常消息略有不同，包括合并配置更新消息和入队列操作。

```go
config, configSeq, err := processor.ProcessConfigUpdateMsg(msg) // 合并配置更新消息
processor.Configure(config, configSeq) //入队列操作
```

合并配置更新消息方法会映射到 orderer.common.msgprocessor 包中 StandardChannel 结构体的 `ProcessConfigUpdateMsg(env *cb.Envelope) (configSeq uint64, err error)` 方法，计算合并后的配置和配置编号，实现如下。

```go
func (s *StandardChannel) ProcessConfigUpdateMsg(env *cb.Envelope) (config *cb.Envelope, configSeq uint64, err error) {
    logger.Debugf("Processing config update message for channel %s", s.support.ChainID())

    seq := s.support.Sequence()
    err = s.filters.Apply(env)
    if err != nil {
        return nil, 0, err
    }

    configEnvelope, err := s.support.ProposeConfigUpdate(env)
    if err != nil {
        return nil, 0, err
    }

    config, err = utils.CreateSignedEnvelope(cb.HeaderType_CONFIG, s.support.ChainID(), s.support.Signer(), configEnvelope, msgVersion, epoch)
    if err != nil {
        return nil, 0, err
    }

    err = s.filters.Apply(config)
    if err != nil {
        return nil, 0, err
    }

    return config, seq, nil
}
```

入队列操作会根据 consensus 配置的不同映射到 orderer.consensus.solo 包或 orderer.consensus.kafka 包中的方法。

以 kafka 情况为例，会映射到 chainImpl 结构体的对应方法。该方法会将消息进一步封装为 `sarama.ProducerMessage` 类型消息，通过 enqueue 方法发给 Kafka 后端。

```go
func (chain *chainImpl) Configure(config *cb.Envelope, configSeq uint64) error {
    marshaledConfig, err := utils.Marshal(config)
    if err != nil {
        return fmt.Errorf("cannot enqueue, unable to marshal config because = %s", err)
    }
    if !chain.enqueue(newConfigMessage(marshaledConfig, configSeq)) {
        return fmt.Errorf("cannot enqueue")
    }
    return nil
}
```

##### 返回响应

如果处理成功，则返回成功响应消息。

```go
err = srv.Send(&ab.BroadcastResponse{Status: cb.Status_SUCCESS})
```



