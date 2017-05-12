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

* 调用 ValidateProposalMessage() 方法对签名的提案进行格式检查，主要检查 Channel头（是否合法头部类型）、签名头（是否包括了 nonce和creators 数据），检查签名域（creator是合法证书，签名是否正确）。
* 如果是系统 CC，检查是否是可以从外部调用的三种之一：cscc、lscc 或 qscc。