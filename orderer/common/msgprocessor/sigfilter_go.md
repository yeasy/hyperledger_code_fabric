#### sigfilter.go

签名过滤器的实现

```
// SigFilterSupport provides the resources required for the signature filter
type SigFilterSupport interface {
	// PolicyManager returns a reference to the current policy manager
	PolicyManager() policies.Manager
}

// SigFilter stores the name of the policy to apply to deliver requests to
// determine whether a client is authorized
type SigFilter struct {
	policyName string
	support    SigFilterSupport
}
```

NewSigFilter创建一个签名过滤器实例。

#### Apply

* 取出过滤器的过滤策略policy
* 使用策略来评估消息是否符合策略，返回拒绝、转发、永不接收。



