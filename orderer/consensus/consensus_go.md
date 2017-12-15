### consensus.go
共识器的接口。

```golang
type Consenter interface {
	HandleChain(support ConsenterSupport, metadata *cb.Metadata) (Chain, error)
}
```

初始化一个静态的链结构，如 ordererconsensus.kafka.chainImpl 结构。