### package.go
响应 `peer chaincode package` 命令，将某个 chaincode 打包为 deployment 的 spec。

例如

```bash
$ peer chaincode package -n test_cc  -o orderer0:7050 -c '{"Args":["init","a","100","b","200"]}' -v 1.1 
```

命令会调用 chaincodePackage。

首先会调用 InitCmdFactory，初始化 Signer 等结构。这一步对于所有 chaincode 子命令来说都是类似的，个别会初始化不同的结构。

之后主要过程如下：

* 根据传入的各种参数，生成 ChaincodeSpec。
* 生成 ChaincodeDeploymentSpec 结构。
* 根据 CDS、签名实体、策略、通道、escc、vscc 等信息，创建一个 LSCC 的 ChaincodeInvocationSpec，根据这个 CIS，添加上 TxID（随机数+签名实体，进行 hash），创建一个 Proposal 出来；
* 根据签名实体，对 Proposal 进行签名。
* 调用 EndorserClient，发送 gprc 消息，将签名后 Proposal 发给指定的 peer。
* 根据 peer 的返回，创建一个 Envelop 结构（SignedTx）并进行签名。
* 将 Envelop 通过 grpc 通道发给 orderer。

