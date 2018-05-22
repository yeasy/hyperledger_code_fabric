#### filter.go

消息过滤器的实现。

```
type Rule interface {
	// Apply applies the rule to the given Envelope, either successfully or returns error
	Apply(message *ab.Envelope) error
}
type RuleSet struct {
	rules []Rule
}
```

NewRuleSet函数使用一个规则列表创建规则集合。

#### Apply

规则集合apply函数按照顺序执行所有Rule的过滤规则，最后返回消息是否有效。



