### deliverevents.go

处理与账本有关的deliver event。

```go
type server struct {
	dh                    deliver.Handler
	policyCheckerProvider PolicyCheckerProvider
}
```

server是处理deliver event服务器，使用deliver handler发送广播消息。

#### NewDeliverEventsServer

新建一个deliver event服务，在peer node start命令启动的peer节点初始化时创建，用来传送区块和过滤的区块事件。

#### DeliverFiltered

在账本提交后，向client发送区块流。主要用来处理过滤的区块事件。

#### Deliver

在账本提交后，向client发送区块流。主要用来处理传送区块。



