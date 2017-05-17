## channel

包括 create、fetch、join、list 等命令。

所有命令都会先执行初始化方法 InitCmdFactory，初始化需要的 EndorserClient 或 OrdererClient。

* create：创建一个新的 channel。根据指定的配置交易文件路径或默认配置，创建 Envelope 结构，发送给 Orderer，并将所指定创建通道中的初始区块写到本地文件 chainID.block。
* fetch：获取指定通道的初始区块。从 Orderer 获取指定通道的初始区块，写到本地文件 chainID.block。
* join：让 peer 加入某个通道。读取本地的 block 文件，生成一个 cscc 的 JoinChain 交易 spec，进一步封装为一个 ChaincodeInvocationSpec，创建 CONFIG 类型的 proposal，并签名，通过 EndorserClient 发给 peer。
* list：列出 peer 所加入的所有通道。生成一个 cscc 的 GetChannels 交易 spec，进一步封装为一个 ChaincodeInvocationSpec，创建 ENDORSER_TRANSACTION 类型的 proposal，并签名，通过 EndorserClient 发给 peer。


各子命令的参数支持情况如下。

| 命令 | -b 区块文件路径 |-c chainID| -f 配置交易文件路径 | -o Orderer | --tls | --cafile tls 证书路径 |
| -- | -- | -- | -- | -- | -- | -- |
| create | 不支持 | 必需 | 可选 | 必需 | 可选 | 可选 |
| fetch| 不支持 | 必需 | 不支持 | 必需 | 可选 | 可选 |
| join | 必需 | 不支持 | 不支持 | 不支持 | 不支持 | 不支持 |
| list | 不支持 | 不支持 | 不支持 | 不支持 | 不支持 | 不支持 |