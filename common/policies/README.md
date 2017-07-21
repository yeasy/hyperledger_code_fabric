## policies
策略的处理。

策略目前包括 ImplicitMetaPolicy 和 SignaturePolicy 两种。


* ImplicitMetaPolicy：基于其它策略（往往是子空间中策略）的结果来判断。
* SignaturePolicy：基于签名的策略，签名集合中某种特定组合符合特定条件。如至少 3 个签名，其中 1 个管理员，2 个成员。


```go
type implicitMetaPolicy struct {
	conf        *cb.ImplicitMetaPolicy
	threshold   int
	subPolicies []Policy
}
```