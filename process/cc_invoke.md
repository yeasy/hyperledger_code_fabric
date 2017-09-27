## 链码调用

实现上，基本过程如下：

* 首先，也是要创建一个 SignedProposal 消息。根据传入的各种参数，生成 ChaincodeSpec 结构（其中，Input 为传入的调用参数）。然后，根据 ChaincodeSpec、chainID、签名实体等，生成 ChaincodeInvocationSpec 结构。进而封装生成 Proposal 结构（通道头部中类型为 ENDORSER_TRANSACTION），并进行签名。
* 调用 EndorserClient，发送 gRPC 消息，将签名后的 Proposal 发给指定的 Peer 节点（Endorser），调用 `ProcessProposal(ctx context.Context, in *SignedProposal, opts ...grpc.CallOption) (*ProposalResponse, error)` 方法，进行背书处理。节点会模拟运行链码调用交易，成功后会返回 ProposalResponse 消息（带有背书签名）。
* 根据 Peer 返回的 ProposalResponse 消息，创建一个 SignedTX（Envelop 结构的交易，带有签名）。
* 使用 BroadcastClient 将交易消息通过 gRPC 通道发给 Orderer 进行全网排序并广播给 Peer 进行确认提交。

注意 invoke 是异步操作，invoke 成功只能保证交易已经进入 Orderer 进行排序，但无法保证最终写到账本中（例如交易未通过 Committer 验证而被拒绝）。需要通过 eventHub 或查询方式来进行确认交易是否最终写入到账本上。