### signpackage.go

响应 `peer chaincode signpackage` 命令，将某个 chaincode 打包后的package进行签名。

例如

```bash
$ peer chaincode signpackage <inputpackage> <outputpackage>
```

命令会调用 signpackage方法。

首先会调用 InitCmdFactory，初始化 Signer 等结构。这一步对于所有 chaincode 子命令来说都是类似的，个别会初始化不同的结构。

之后主要过程如下：

* 根据传入的各种参数，生成 ChaincodeSpec。
* 生成 ChaincodeDeploymentSpec 结构。
* 根据 CDS 等创建 签名后的 ChaincodeDeploymentSpec，并封装为 Envelop 结构（其中数据是一个 SignedChaincodeDeploymentSpec）。
* 将 Envelop 结构写到本地指定的文件中。



