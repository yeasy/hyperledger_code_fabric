### common.go

提供一些通用方法。

#### checkspec

检查chaincode详细描述有效性。

#### getChaincodeDeploymentSpec

获得chaincode部署详细描述。

* checkSpec检查chaincode描述

* GetChaincodePackageBytes获得docker容器中不同运行平台的chaincode字节码

* 构造pb.ChaincodeDeploymentSpec结构返回

#### getChaincodeSpec

* checkChaincodeCmdParams检查命令参数有效性

* 从cli cmd获得chaincode详细描述，具体的命令参数已经解析放入了chaincode.go里面定义的那些全局变量，把这些全局变量汇总成结构pb.ChaincodeSpec。

#### chaincodeInvokeOrQuery

* 获取链码详细信息（含有发出的指令里的信息，比如-C 通道、-n 名字、-c 参数）。
* ChaincodeInvokeOrQuery\(\)（创建proposal，对proposal进行签名，给endorser去执行proposal，拿到proposalResponse后组合成完整的签名交易，类型是一个Envelope，将envelope发给orderer去排序，返回proposalResponse）。
* 检查proposalResponse。
* 返回nil。 



