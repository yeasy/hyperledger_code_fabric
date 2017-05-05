### package.go
响应 `peer chaincode package` 命令，将某个 chaincode 打包为 deployment 的 spec。

例如

```bash
$ peer chaincode package -n test_cc -c '{"Args":["init","a","100","b","200"]}' -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02 -v 1.0  test_cc_1.0.pkg
```

命令会调用 chaincodePackage 方法。

首先会调用 InitCmdFactory，初始化 Signer 等结构。这一步对于所有 chaincode 子命令来说都是类似的，个别会初始化不同的结构。

之后主要过程如下：

* 根据传入的各种参数，生成 ChaincodeSpec。
* 生成 ChaincodeDeploymentSpec 结构。
* 根据 CDS 等创建 签名后的 ChaincodeDeploymentSpec，并封装为 Envelop 结构（其中数据是一个 SignedChaincodeDeploymentSpec）。
* 将 Envelop 结构写到本地指定的文件中。

