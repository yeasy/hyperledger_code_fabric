### cauthdsl.go

主要实现了 `func compile(policy *cb.SignaturePolicy, identities []*mb.MSPPrincipal, deserializer msp.IdentityDeserializer) (func([]*cb.SignedData, []bool) bool, error) ` 方法。

该方法根据传入的策略结构，返回一个函数 `func([]*cb.SignedData, []bool) bool, error`，作为策略 evaluate 方法。