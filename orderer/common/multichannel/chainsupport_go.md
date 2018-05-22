#### chainsupport.go

ChainSupport 结构是单个通道的管理句柄。

```
// ChainSupport holds the resources for a particular channel.
type ChainSupport struct {
	*ledgerResources
	msgprocessor.Processor
	*BlockWriter
	consensus.Chain
	cutter blockcutter.Receiver
	crypto.LocalSigner
}
```



