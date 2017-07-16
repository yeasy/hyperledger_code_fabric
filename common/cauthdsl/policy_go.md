### policy.go

实现上，一条 policy 结构实现一个 evalutor 方法。

```go
type policy struct {
	evaluator func([]*cb.SignedData, []bool) bool
}
```

对外提供的方法是 Evaluate() 方法，调用了 evaluator() 私有方法。

```go
func (p *policy) Evaluate(signatureSet []*cb.SignedData) error {
	if p == nil {
		return fmt.Errorf("No such policy")
	}

	ok := p.evaluator(signatureSet, make([]bool, len(signatureSet)))
	if !ok {
		return errors.New("Failed to authenticate policy")
	}
	return nil
}
```

同时，提供了 NewPolicyProvider(deserializer msp.IdentityDeserializer) policies.Provider 方法，作为工厂方法。