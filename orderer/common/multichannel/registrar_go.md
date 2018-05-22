#### registrar.go

Registrar 结构是网络中所有channel的管理句柄。

```
// Registrar serves as a point of access and control for the individual channel resources.
type Registrar struct {
	chains          map[string]*ChainSupport
	consenters      map[string]consensus.Consenter
	ledgerFactory   blockledger.Factory
	signer          crypto.LocalSigner
	systemChannelID string
	systemChannel   *ChainSupport
	templator       msgprocessor.ChannelConfigTemplator
	callbacks       []func(bundle *channelconfig.Bundle)
}
```



