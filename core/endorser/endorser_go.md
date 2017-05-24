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
* 如果 chainID 不为空，调用 simulateProposal\(\) 方法获取模拟执行的结果，对于用户链码交易，进行背书。
* 返回响应消息 ProposalResponse。



