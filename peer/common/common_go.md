### common.go

peer公共变量和通用函数实现。

#### init

模块初始化函数，初始化了3个跟orderer通信的client和签名的函数变量。

#### InitConfig

初始化配置管理模块viper

#### InitCrypto

初始化peer的加密功能

#### SetBCCSPKeystorePath

设置bccsp密钥保存路径。

#### GetDefaultSigner

获得默认的签名实体。

#### GetOrdererEndpointOfChain

获得区块链上所有的orderer端点。是使用cscc chaincode调用来查询到的。

* 构造ChaincodeInvocationSpec描述结构，chaincode name是系统chaincode cscc
* 创建proposal并进行签名
* 使用endorserClient.ProcessProposal提交给endorser进行处理

* 从应答结果取出配置区块，并从区块中取出所有的orderer地址进行返回



#### configFromEnv

根据传入的参数使用配置模块viper从配置文件里读出各种配置。

