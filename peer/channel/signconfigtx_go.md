### signconfigtx.go

`peer channel`signconfigtx命令的入口。

命令处理函数是sign，首先通过 InitCmdFactory 进行初始化，然后再对传入的configtx文件进行签名，流程如下。

* 读取configtx文件
* 调用CreateProposalFromCIS创建交易proposal
* 对proposal进行签名
* EndorserClient.ProcessProposal发送给eddorser进行背书
* 获得chaincode执行结果，解码获得BlockchainInfo结构信息进行返回



