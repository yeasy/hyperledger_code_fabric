### update.go

`peer channel update`命令的入口。

命令处理函数是update，首先通过 InitCmdFactory 进行初始化，后续流程如下。

* 读取configtx文件
* UnmarshalEnvelope解码文件信息，输出到envelope结构
* sanityCheckAndSignConfigTx对envelope进行签名
* 编码签名后的envelope信息
* 调用broadcastClient.Send通知orderer进行更新



