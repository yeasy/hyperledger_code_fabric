### chainsupport.go
#### Chain 接口

```go
type Chain interface {
	// 收到一个消息
	Enqueue(env *cb.Envelope) bool

	// 启动服务，从排序者获取消息，切分为区块，写到账本
	Start()

	// 释放资源
	Halt()
}
```

#### ChainSupport 接口

继承自 ConsenterSupport。

```go
type ChainSupport interface {
	// This interface is actually the union with the deliver.Support but because of a golang
	// limitation https://github.com/golang/go/issues/6977 the methods must be explicitly declared

	// PolicyManager returns the current policy manager as specified by the chain config
	PolicyManager() policies.Manager

	// Reader returns the chain Reader for the chain
	Reader() ledger.Reader

	broadcast.Support
	ConsenterSupport

	// ProposeConfigUpdate applies a CONFIG_UPDATE to an existing config to produce a *cb.ConfigEnvelope
	ProposeConfigUpdate(env *cb.Envelope) (*cb.ConfigEnvelope, error)
}
```



#### chainSupport 结构体

```go
type chainSupport struct {
	*ledgerResources
	chain         Chain
	cutter        blockcutter.Receiver
	filters       *filter.RuleSet
	signer        crypto.LocalSigner
	lastConfig    uint64
	lastConfigSeq uint64
}
```

十分核心的结构体，支持对 Chain 的大量操作。

newChainSupport()方法会返回一个初始化的该结构体。