## Orderer 节点对排序后消息的处理过程

以 Kafka 模式为例，Orderer 节点启动后，会通过 `orderer/consensus/kafka` 模块中 `chainImpl` 结构体的 `processMessagesToBlocks() ([]uint64, error)` 方法，持续处理来自 Kafka 对应分区的消息。


### 主要过程

`chainImpl` 结构体的 `processMessagesToBlocks()` 方法不断从分区中 Consume 消息并进行处理，同时定时发送 TimeToCut 消息。

处理消息类型包括 Connect 消息（Producer 启动后发出）、TimeToCut 消息和 Regular 消息（Fabric 消息）。分别调用对应方法进行处理，主要流程如下：

```go
for {
	select {
		case <-chain.haltChan: // 链故障了，退出
		case kafkaErr := <-chain.channelConsumer.Errors(): //获取 Kakfa 消息发生错误
			select {
				case <-chain.errorChan: // 连接关闭，不进行任何操作
				default: //其它错误，OutofRange，关闭 errorChan；否则进行超时重连
			}
			select {
				case <-chain.errorChan: // 连接仍然关闭，尝试后台进行重连
			}
		case <-topicPartitionSubscriptionResumed: // 继续
		case <-deliverSessionTimedOut: //访问超时，尝试后台进行重连
		case in, ok := <-chain.channelConsumer.Messages(): // 成功读取到消息，进行处理，核心过程
			switch msg.Type.(type) {
			case *ab.KafkaMessage_Connect: // Kafka 连接消息，忽略
			case *ab.KafkaMessage_TimeToCut: // TTC，处理过去一批消息为区块
			case *ab.KafkaMessage_Regular: // 核心处理：Fabric 相关消息
				chain.processRegular(msg.GetRegular(), in.Offset)
		case <-chain.timer: //定期发出 TimeToCut 消息到 Kafka
	}
}
```

### Fabric 相关消息的处理

对于 Fabric 相关消息（包括交易消息和配置消息），具体会调用 chainImpl 结构体的 `processRegular(regularMessage *ab.KafkaMessageRegular, receivedOffset int64) error` 方法进行处理。

该方法的核心代码如下：

```go
func (chain *chainImpl) processRegular(regularMessage *ab.KafkaMessageRegular, receivedOffset int64) error {
	env := &cb.Envelope{}
	proto.Unmarshal(regularMessage.Payload, env) // 从载荷中解析出信封结构
	switch regularMessage.Class {
		case ab.KafkaMessageRegular_NORMAL: // 普通交易消息
			chain.ProcessNormalMsg(env) // 检查消息合法性，分应用链和系统链两种情况
			commitNormalMsg(env) // 处理交易消息，满足条件则切块，并写入本地账本
		case ab.KafkaMessageRegular_CONFIG: // 配置消息，包括通道头部类型为 CONFIG、CONFIG_UPDATE、ORDERER_TRANSACTION 三种
			chain.ProcessConfigMsg(env) //检查消息合法性，分应用链和系统链两种情况
			commitConfigMsg(env) // 切块，写入账本。如果是 ORDERER_TRANSACTION 消息，创建新的应用通道账本；如果是 CONFIG 消息，更新配置。
}
```

#### 普通交易消息

普通交易消息，会检查是否满足生成区块的条件，满足则产生区块并写入本地账本结构。通过内部的 `commitNormalMsg(env)` 方法来完成。

主要调用 `orderer/common/multichannel` 模块中 `BlockWriter` 结构体的 `CreateNextBlock(messages []*cb.Envelope) *cb.Block` 方法和 `WriteBlock(block *cb.Block, encodedMetadataValue []byte)` 方法。


`CreateNextBlock(messages []*cb.Envelope) *cb.Block` 方法基本过程十分简单，创建新的区块，将传入的交易的信封结构直接序列化到 `block.Data.Data[]` 域中。

```go
func (bw *BlockWriter) CreateNextBlock(messages []*cb.Envelope) *cb.Block {
	previousBlockHash := bw.lastBlock.Header.Hash()

	data := &cb.BlockData{
		Data: make([][]byte, len(messages)),
	}

	var err error
	for i, msg := range messages {
		data.Data[i], err = proto.Marshal(msg)
		if err != nil {
			logger.Panicf("Could not marshal envelope: %s", err)
		}
	}

	block := cb.NewBlock(bw.lastBlock.Header.Number+1, previousBlockHash)
	block.Header.DataHash = data.Hash()
	block.Data = data

	return block
}
```
`WriteBlock(block *cb.Block, encodedMetadataValue []byte)` 方法则将 Kafka 相关的元数据也附加到区块结构中，添加区块的签名、最新配置的签名，并写入到本地账本。

```go
func (bw *BlockWriter) WriteBlock(block *cb.Block, encodedMetadataValue []byte) {
	bw.committingBlock.Lock()
	bw.lastBlock = block

	go func() {
		defer bw.committingBlock.Unlock()
		bw.commitBlock(encodedMetadataValue)
	}()
}
```

Kafka 相关的元数据（KafkaMetadata）包括：

* LastOffsetPersisted：上次消息的偏移量；
* LastOriginalOffsetProcessed：本条消息被重复处理时，最新的偏移量；
* LastResubmittedConfigOffset：上次提交的配置消息的偏移量。

#### 配置消息


配置消息，处理过程跟普通消息类似，会检查是否满足生成区块的条件，满足则产生区块并写入本地账本结构。通过内部的 `commitConfigMsg(env)` 方法来完成。

逻辑上唯一不同的的是，每个配置消息会单独生成区块。因此，如果存在已有的交易消息，会先把这些消息生成区块。

主要调用 `orderer/common/multichannel` 模块中 `BlockWriter` 结构体的 `CreateNextBlock(messages []*cb.Envelope) *cb.Block` 方法和 `WriteConfigBlock(block *cb.Block, encodedMetadataValue []byte)` 方法。

