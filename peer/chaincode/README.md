## chaincode

包括 install、instantiate、invoke、query 等命令，大家的过程都是类似的，创建 Proposal，发给 peer，获取 Response。

instantiate、upgrade、invoke 等子命令还需要根据 Response 创建 SignedTX，发送给 Orderer。

package、signpackage 子命令则是本地操作。

各子命令的全局参数支持情况如下。

| 命令 | -C 通道 | -c cc 参数 | -E escc | -n 名称 | -o Orderer | -p 路径 | -P policy | -v 版本 | -V vscc |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| install | 不支持 | 支持 | 不支持 | 必需 | 不支持 | 必需 | 不支持 | 必需 | 不支持 |
| instantiate | 必需 | 必需 | 支持 | 必需 | 支持 | 不支持 | 支持 | 必需 | 支持 |
| upgrade | 必需 | 必需 | 支持 | 必需 | 支持 | 不支持 | 不支持 | 必需 | 支持 |
| package | 不支持 | 支持 | 不支持 | 必需 | 不支持 | 必需 | 不支持 | 必需 | 不支持 |
| invoke | 支持 | 必需 | 不支持 | 必需 | 支持 | 不支持 | 不支持 | 不支持 | 不支持 |
| query | 支持 | 必需 | 不支持 | 必需 | 不支持 | 不支持 | 不支持 | 不支持 | 不支持 |

* 不支持：代表该参数即便被指定，也不会被使用。
* 支持：代表该参数可以被使用，如果不指定，则可能采取默认值或自动获取。
* 必需：该参数必需被指定。



