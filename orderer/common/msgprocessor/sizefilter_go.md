#### sizefilter.go

消息长度过滤器实现。

```
// Support defines the subset of the channel support required to create this filter
type Support interface {
	BatchSize() *ab.BatchSize
}

// MaxBytesRule implements the Rule interface.
type MaxBytesRule struct {
	support Support
}
```

NewSizeFilter创建一个消息过滤器，在消息超过最大长度时拒绝消息。

Apply取出配置的最大消息长度，与收到的消息长度比较，超出则报错。

