### peer.go

#### peer 相关

比较核心的数据结构。

```go
type chainSupport struct {
    bundleSource *resourcesconfig.BundleSource
    channelconfig.Resources
    channelconfig.Application
    ledger     ledger.PeerLedger
    fileLedger *fileledger.FileLedger
}
```

核心方法，包括：

* Apply：尝试将一个configtx配置应用到新配置中；
* Ledger：返回peer账本；
* GetMSPIDs：返回channel的MSPid；
* Sequence：返回序列号。
* Reader：返回区块链的读取者reader；
* Errored：返回一个通道，该通道在后台接收方出错时关闭；

#### chain相关

两个基础结构

```go
// chain is a local struct to manage objects in a chain
type chain struct {
    cs        *chainSupport
    cb        *common.Block
    committer committer.Committer
}

// chains is a local map of chainID->chainObject
var chains = struct {
    sync.RWMutex
    list map[string]*chain
}{list: make(map[string]*chain)}
```

#### Initialize

peer初始化核心方法之一，在账本和gossip服务ready后调用，是在peer node start命令中调用的此函数。功能是从永久存储中恢复并建立所有的区块链。

* 首先创建一个新的加权信号量，其有并发访问的最大组合权重；
* 区块链初始化函数赋值；
* 区块链账本管理器初始化，并取出所有账本id；针对每个账本进行如下操作：打开账本，读取账本配置块，使用配置块创建账本的区块链，初始化区块链。

#### getCurrConfigBlockFromLedger

从账本中读取区块链配置块。

* 先用账本取得区块链的基本信息；
* 再使用GetBlockByNumber获得最后一个区块；
* 从最后一个区块的metadata中获得最后一个配置区块的index
* 最后使用GetBlockByNumber获得最后一个配置区块返回

#### createChain

使用账本配置和区块配置创建一个区块链chain，并插入到本地的chains链表中。

依次初始化chainSupport和commiter，然后加入配置块构成chain结构。这其中包含了p2p协议gossip的初始化和事件处理函数配置。





