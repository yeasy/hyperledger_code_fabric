## cauthdsl

实现 Policy 相关的数据结构和方法。

fabric 中，策略主要分为签名策略（SignaturePolicy）和隐式策略（ImplicitMetaPolicy）两种。

最常见的签名策略采用 SignaturePolicyEnvelope 结构来表达。定义在 [protos/common/policies.go](protos/common/policies.go)中。

```go
type SignaturePolicyEnvelope struct {
	Version    int32                   `protobuf:"varint,1,opt,name=version" json:"version,omitempty"`
	Rule       *SignaturePolicy        `protobuf:"bytes,2,opt,name=rule" json:"rule,omitempty"`
	Identities []*common1.MSPPrincipal `protobuf:"bytes,3,rep,name=identities" json:"identities,omitempty"`
}
```

签名策略的规则为检查签名是否满足指定的 principal（特定个体、身份等）。

更通用的 Implicit 策略是一个递归结构，可以指定依赖其它策略，最终底层为签名策略。