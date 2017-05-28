### list.go

同样的，首先通过 InitCmdFactory 进行初始化。


主要实现过程如下：

* 客户端首先创建一个 ChaincodeSpec 结构，其 input 中的 Args 第一个参数是 CSCC.GetChannels（指定调用配置链码的操作）。
* 利用 CS 构造一个 ChaincodeInvocationSpec 结构。
* 利用 CIS，创建 Proposal 结构并进行签名，channel 头部类型为 ENDORSER_TRANSACTION。
* 客户端通过 gRPC 将 Proposal 发给 Endorser（所操作的 Peer），调用 ProcessProposal() 方法进行处理，主要是通过配置系统链码查询本地链信息并返回。
* 命令执行成功后，客户端会受到来自 Peer 端的回复消息，从其中提取出应用通道列表信息并输出。

