### getinfo.go

`peer channel`getinfo命令的入口。

命令处理函数是getinfo，首先通过 InitCmdFactory 进行初始化，然后再调用getBlockChainInfo获取信息，流程如下。

* 构造qscc类型的chaincode执行描述结构 ChaincodeInvocationSpec
* 调用CreateProposalFromCIS创建交易proposal
* 对proposal进行签名
* EndorserClient.ProcessProposal发送给eddorser进行背书
* 获得chaincode执行结果，解码获得BlockchainInfo结构信息进行返回



