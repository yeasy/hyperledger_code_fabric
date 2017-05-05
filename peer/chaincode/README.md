## chaincode
包括 install、instantiate、invoke、query 等命令，大家的过程都是类似的，创建 Proposal，发给 peer，获取 Response。

instantiate、upgrade、invoke 等子命令还需要根据 Response 创建 SignedTX，发送给 Orderer。

package、signpackage 子命令则是本地操作。

各子命令的全局参数支持情况如下。

| 命令 |-C 通道| -c cc 参数| -E escc | -n 名称 | -o Orderer | -p 路径 | -P policy | -v 版本 | -V vscc |
|-|-|-|-|-|-|-|-|-|
| install|不支持| 可选 |不支持|必需|不支持|必需|不支持| 必需 | 不支持 |
| instantiate|可选| 必需 |可选|必需|必需|不支持|可选|必需 | 可选 |
| upgrade|不支持| 必需 |可选|必需|必需|必需|可选| 必需| 可选 |
| package|不支持| 可选 |不支持|必需|不支持|必需|不支持|必需 | 不支持|
| signpackage|不支持| 可选 |不支持|必需|不支持|必需|不支持|必需 | 不支持|
| invoke |不支持| 必需 |不支持|必需|必需|不支持|不支持|不支持|不支持 |
| query |不支持| 必需 |不支持|必需|不支持|不支持|不支持|不支持|不支持 |