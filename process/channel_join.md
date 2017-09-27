## 通道加入

主要步骤包括：

* 客户端首先创建一个 ChaincodeSpec 结构，其 input 中的 Args 第一个参数是 CSCC.JoinChain（指定调用配置链码的操作），第二个参数为所加入通道的初始区块。
* 利用 ChaincodeSpec 构造一个 ChaincodeInvocationSpec 结构。
* 利用 ChaincodeInvocationSpec，创建 Proposal 结构并进行签名，channel 头部类型为 CONFIG。
* 客户端通过 gRPC 将 Proposal 签名后发给 Endorser（所操作的 Peer），调用 `ProcessProposal(ctx context.Context, in *SignedProposal, opts ...grpc.CallOption) (*ProposalResponse, error)` 方法进行处理，主要通过配置系统链码进行本地链的初始化工作。
* 初始化完成后，即可收到来自通道内的 Gossip 消息等。

其中，比较重要的数据结构包括 ChaincodeSpec、ChaincodeInvocationSpec、Proposal 等。