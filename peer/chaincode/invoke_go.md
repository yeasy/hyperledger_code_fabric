### invoke.go

响应 `peer chaincode invoke` 命令，执行某个 chaincode 中操作。

例如

```bash
peer chaincode invoke -n test_cc -c '{"Args":["invoke","a","b","10"]}' -o orderer0:7050
```

命令会调用 chaincodeInvoke。

首先会调用 InitCmdFactory，初始化 Endosermentclient、Signer 等结构。这一步对于所有 chaincode 子命令来说都是类似的，个别会初始化不同的结构。

之后调用 chaincodeInvokeOrQuery 方法。

chaincodeInvokeOrQuery 方法主要过程如下：

* 生成 ChaincodeSpec。
* 根据 CS、chainID、签名实体等，生成 ChaincodeInvocationSpec。
* 根据 CIS，生成 Proposal，并进行签名。
* 签名后，通过 endorserClient 发送给指定的 peer。
* 利用获取到的 Response，创建 SignedTX，发送给 orderer。
* 成功的话，输出成功消息，注意 invoke 是异步操作，无法获取到执行结果。

注意 invoke 和 query 的区别，query 不需要创建 SignedTx 发送到 orderer，而且会返回结果。

