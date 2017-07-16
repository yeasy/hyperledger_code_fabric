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

* 调用 ValidateProposalMessage\(\) 方法对签名的提案进行格式检查，主要检查 Channel头（是否合法头部类型）、签名头（是否包括了 nonce和creators 数据），检查签名域（creator是合法证书，签名是否正确）。
* 如果是系统 CC，检查是否是可以从外部调用的三种之一：cscc、lscc 或 qscc。
* 如果 chainID 不为空，获取对应 chain 的账本结构，检查 TxID 在账本上没出现过；对于非系统 CC，检查 ACL（根据 chaincode 指定的 endorsement Policy，签名提案在指定 channel 上有写权限，最终是调用 common/cauthdsl 下面代码，支持指定必须包括某个成员来签名，或者是凑够若干几个合法签名）。
* 如果 chainID 不为空，调用 simulateProposal\(\) 方法获取模拟执行的结果，检查返回的响应response的状态，若不小于错误500则创建并返回一个失败的ProposalResponse。
* chainID不为空下，调用endorseProposal\(\)方法对之前得到的模拟执行的结果进行背书，返回ProposalResponse，检查simulateProposal返回的response的状态，若不小于错误阈值400（被背书节点反对），返回ProposalResponse及链码错误chaincodeError。
* 将response.Payload赋给ProposalResponse.Response.Payload（因为simulateProposal返回的response里面包含链码调用的结果）。
* 返回响应消息 ProposalResponse。

#### simulateProposal 方法

主要过程如下：

* 获取ChaincodeInvocationSpec，里面包含了链码调用时传入的各种参数。
* 检查ESCC和VSCC（TODO）
* 不是系统链码的话，检查是否实例化，并检验本地文件系统得到的实例化Policy是否跟LSCC得到的实例化Policy匹配。
* 执行Proposal，调用callChaincode\(\)方法（主要内容见core/chaincode/exectransaction.go下的Execute\(\)），检查response.Status，最后返回response和ccevent。
* 对transactionSimulator执行GetTxSimulationResults\(\)拿到交易读写集simResult。
* 返回链码数据ChaincodeData（LSCC中的）、响应response、交易读写集simResult、链码事件信息ccevent。

#### endorseProposal 方法

主要过程如下：

* 获取被调用的链码指定的背书链码的名字。
* 通过callChaincode\(\)（主要内容见core/chaincode/exectransaction.go下的Execute\(\)）实现对背书链码的调用，返回响应response（对ESCC的调用同样也会产生simulation results，但ESCC不能背书自己产生的simulation results，需要背书最初被调用的链码产生的simulation results）。
* 将response.Payload解码后返回。


