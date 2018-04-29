### list.go

响应 `peer chaincode list`命令，执行某个 chaincode 中操作。

例如

```bash
peer chaincode list --installed -C mychannel
```

命令会调用 getChaincodes。

首先会调用 InitCmdFactory，初始化 Endosermentclient、Signer 等结构。这一步对于所有 chaincode 子命令来说都是类似的，个别会初始化不同的结构。

之后根据传入的参数判断是查询已安装化的chaincode，则调用 CreateGetInstalledChaincodesProposal方法；判断是查询channel中已实例化的chaincode，则调用 CreateGetChaincodesProposal方法创建对应的proposal。

签名后，通过 endorserClient 发送给指定的 peer。

获得返回结果进行输出。



