### common.go

提供一些通用方法。

#### checkspec

根据不同的运行语言检查chaincode详细描述有效性。

#### getChaincodeDeploymentSpec

获得chaincode部署详细描述。

* checkSpec检查chaincode描述

* GetChaincodePackageBytes获得docker容器中不同运行语言下的chaincode代码，如golang会将代码压缩为.tar.gz文件，具体见core/platforms。

* 构造pb.ChaincodeDeploymentSpec结构返回

#### getChaincodeSpec

* checkChaincodeCmdParams检查命令参数有效性

* 从cli cmd获得chaincode详细描述，具体的命令参数已经解析放入了chaincode.go里面定义的那些全局变量，把这些全局变量汇总成结构pb.ChaincodeSpec。

#### chaincodeInvokeOrQuery\(cmd \*cobra.Command, args \[\]string, invoke bool, cf \*ChaincodeCmdFactory\)

* 获取链码详细信息（含有发出的指令里的信息，比如-C 通道、-n 名字、-c 参数）。
* ChaincodeInvokeOrQuery\(\)（创建proposal，对proposal进行签名，给endorser去执行proposal，拿到proposalResponse后组合成完整的签名交易，类型是一个Envelope，将envelope发给orderer去排序，返回proposalResponse）。
* 如果chaincode是要执行的，检查proposalResponse，调用GetChaincodeAction进一步执行。
* 返回nil。 

#### getCollectionConfigFromFile

#### getCollectionConfigFromBytes

从文件中读取配置集合

#### InitCmdFactory

初始化chaincode命令工厂，

* 如果需要跟endorser通信，那么创建endorserClient，参见peerclient.go的NewPeerClientFromEnv函数。
* 获取默认的签名实体
* 如果需要跟orderer通信，那么创建跟orderer交互的client，这里还有一层判断，如果配置指定了orderer的地址，那么使用GetOrdererEndpointOfChainFnc获取，如果指定了orderer地址，那么使用GetBroadcastClientFnc获取。



