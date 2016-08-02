### noops.go
一个最简单的 consensus 插件。

最主要的数据结构即 Noops。

```go
// Noops is a plugin object implementing the consensus.Consenter interface.
type Noops struct {
	stack    consensus.Stack  // 一些句柄
	txQ      *txq  // 交易放在这个队列中
	timer    *time.Timer // 计时器，超时后发送消息进行创建区块的操作
	duration time.Duration // 过了多久
	channel  chan *pb.Transaction // 接收消息：超时；队列中交易达到区块上限
}
```

对外的方法，主要是获取一个 Noops 对象，如果不存在，会调用 newNoops() 来生成。

这个结构启动后，会通过 channel 监听事件（超时；队列中交易达到区块上限）。

当可以处理区块时，调用 processBlock() 方法。

processBlock() 方法进一步调用 processTransactions() 方法，批量执行交易，如果成功则提交并向 NVP 们广播 SYNC_BLOCK_ADDED 消息，失败则回滚。具体实现上，调用的 stack 结构，里面的方法实现在 helper 包中。

#### newNoops 方法

```go
func newNoops(c consensus.Stack) consensus.Consenter {
	var err error
	if logger.IsEnabledFor(logging.DEBUG) {
		logger.Debug("Creating a NOOPS object")
	}
	i := &Noops{}
	i.stack = c
	config := loadConfig()
	blockSize := config.GetInt("block.size")
	blockWait := config.GetString("block.wait")
	if _, err = strconv.Atoi(blockWait); err == nil {
		blockWait = blockWait + "s" //if string does not have unit of measure, default to seconds
	}
	i.duration, err = time.ParseDuration(blockWait)
	if err != nil || i.duration == 0 {
		panic(fmt.Errorf("Cannot parse block wait: %s", err))
	}

	logger.Infof("NOOPS consensus type = %T", i)
	logger.Infof("NOOPS block size = %v", blockSize)
	logger.Infof("NOOPS block wait = %v", i.duration)

	i.txQ = newTXQ(blockSize)

	i.channel = make(chan *pb.Transaction, 100)
	i.timer = time.NewTimer(i.duration) // start timer now so we can just reset it
	i.timer.Stop()
	go i.handleChannels()
	return i
}
```

