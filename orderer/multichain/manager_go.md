### manager.go

#### Manager 接口

负责创建和访问 chain。

```go
type Manager interface {
	// GetChain retrieves the chain support for a chain (and whether it exists)
	GetChain(chainID string) (ChainSupport, bool)

	// SystemChannelID returns the channel ID for the system channel
	SystemChannelID() string

	// NewChannelConfig returns a bare bones configuration ready for channel
	// creation request to be applied on top of it
	NewChannelConfig(envConfigUpdate *cb.Envelope) (configtxapi.Manager, error)
}
```

#### multiLedger 结构

```go
type multiLedger struct {
	chains          map[string]*chainSupport
	consenters      map[string]Consenter
	ledgerFactory   ledger.Factory
	signer          crypto.LocalSigner
	systemChannelID string
	systemChannel   *chainSupport
}
```