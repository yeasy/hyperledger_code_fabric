### signpackage.go

响应 `peer chaincode signpackage` 命令，将某个 chaincode 打包后的package进行签名。

例如

```bash
$ peer chaincode signpackage <inputpackage> <outputpackage>
```

命令会调用 signpackage方法。

首先会调用 InitCmdFactory，初始化 Signer 等结构。这一步对于所有 chaincode 子命令来说都是类似的，个别会初始化不同的结构。

之后主要过程如下：

* 根据传入的需要签名文件参数，读出文件内容，调用UnmarshalEnvelopeOrPanic进行解码。

* 调用SignExistingPackage进行签名，并编码。
* 将编码后内容写到输出文件中。



