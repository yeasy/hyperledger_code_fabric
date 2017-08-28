### endorser.go

提供 Endorser 结构。

```go
// Endorser provides the Endorser service ProcessProposal
type Endorser struct {
    policyChecker policy.PolicyChecker
}
```

最关键的是提供了 ProcessProposal 方法，供 grpc 客户端远程调用，用来接受交易提案，进行背书处理。

#### ProcessProposal 方法

主要过程如下：

* 调用 ValidateProposalMessage() 方法对签名的提案进行格式检查，主要检查 Channel头（是否合法头部类型）、签名头（是否包括了 nonce和creators 数据），检查签名域（creator 是合法证书，签名是否正确）。
* 如果是系统 CC，检查是否是可以从外部调用的三种之一：cscc、lscc 或 qscc。
* 如果 chainID 不为空，获取对应 chain 的账本结构，检查 TxID 在账本上没出现过；对于非系统 CC，检查 ACL（根据 chaincode 指定的 endorsement Policy，签名提案在指定 channel 上有写权限，最终是调用 common/cauthdsl 下面代码，支持指定必须包括某个成员来签名，或者是凑够若干几个合法签名）。
* 如果 chainID 不为空，获取交易模拟器和历史查询器（通过 ledger 去 new txsimulator 和 historyqueryexecutor，而且交易模拟器是不包含历史信息的，所以为了查询历史需要拿到一个 historyqueryexecutor），把 historyqueryexecutor 加入到 Context 的 K-V 储存中。
* 如果 chainID 不为空，调用 simulateProposal() 方法获取模拟执行的结果，检查返回的响应 response的状态，若不小于错误 500 则创建并返回一个失败的 ProposalResponse。
* chainID 不为空下，调用 endorseProposal()方法对之前得到的模拟执行的结果进行背书，返回 ProposalResponse，检查 simulateProposal 返回的response 的状态，若不小于错误阈值 400（被背书节点反对），返回 ProposalResponse 及链码错误 chaincodeError（endorseProposal 里有检查链码执行结果的状态，而 simulateProposal 没有检查）。
* 将 response.Payload 赋给 ProposalResponse.Response.Payload（因为 simulateProposal 返回的 response 里面包含链码调用的结果）。
* 返回响应消息 ProposalResponse。

#### simulateProposal 方法

主要过程如下：

* 获取ChaincodeInvocationSpec，里面包含了链码调用时传入的各种参数。
* 检查 ESCC 和 VSCC（TODO）
* 不是系统链码的话，检查是否实例化，并检验本地文件系统得到的实例化 Policy 是否跟 LSCC 得到的实例化 Policy 匹配。
* 执行 Proposal，调用 callChaincode() 方法，返回 response 和 ccevent。
* 对  transactionSimulator 执行 GetTxSimulationResults()拿到交易读写集 simResult。
* 返回链码数据 ChaincodeData（LSCC 中的）、响应 response、交易读写集 simResult、链码事件信息 ccevent。

#### endorseProposal 方法

主要过程如下：

* 获取被调用的链码指定的背书链码的名字。
* 通过 callChaincode() 实现对背书链码的调用，返回响应 response（对 ESCC 的调用同样也会产生 simulation results，但 ESCC 不能背书自己产生的simulation results，需要背书最初被调用的链码产生的 simulation results）。
* 检查 response.Status，是否大于等于 400（错误阈值），若是则把 response 赋给 proposalResponse.Response 并返回 proposalResponse。
* 将 response.Payload解码后（ProposalResponse类型）返回。

#### callChaincode 方法

主要过程如下：

* 判断交易模拟器，不为空则把它加入到Context的K-V存储中。
* 判断被call的cc是不是系统链码，创建CCContext（包含通道名、链码名、版本号、交易ID、是否 SCC、签名 Prop、Prop）
* 调用 core/chaincode/chaincodeexec.go 下的 ExecuteChaincode()，返回响应 response 和 事件ccevent。
* 返回 response和ccevent。


