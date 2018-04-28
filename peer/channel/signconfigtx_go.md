### signconfigtx.go

`peer channel`signconfigtx命令的入口。

命令处理函数是sign，首先通过 InitCmdFactory 进行初始化，然后再对传入的configtx文件进行签名，流程如下。

* 读取configtx文件
* UnmarshalEnvelope解码文件信息，输出到envelope结构

* sanityCheckAndSignConfigTx对envelope进行签名
* 编码签名后的envelope信息
* 写回原始文件



