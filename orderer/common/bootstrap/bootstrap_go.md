#### bootstrap.go

```
type Helper interface {
	// GenesisBlock should return the genesis block required to bootstrap
	// the ledger (be it reading from the filesystem, generating it, etc.)
	GenesisBlock() *ab.Block
}
```

定义Helper接口，只有一个GenesisBlock方法，其用来返回启动分布式账本所需的创世区块信息，一般是从文件中读取出来。

