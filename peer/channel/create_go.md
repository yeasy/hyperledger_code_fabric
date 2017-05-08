### create.go

`peer channel create` 命令的入口。


![peer channel create](../_images/channel_create.png)

调用 create 方法，首先通过 InitCmdFactory 进行初始化，然后调用 executeCreate。


executeCreate 流程包括：

* 调用 sendCreateChainTransaction，发送创建一个 chain 的交易请求给 orderer；
* 从 orderer 获取到最新的区块；
* 将区块写入到本地的 `chainID + ".block"` 文件。


sendCreateChainTransaction 方法会检查，如果提供了 tx 文件了，则直接读取为 Envelope 结构；如果不存在，则通过默认值来创建一个 Envelope 结构。之后将 Envelope 结构发给 orderer。