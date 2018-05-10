### peer.go

#### peer 相关

比较核心的数据结构。

Peer 接口，两个方法：包括获取一个 peer 端点和发送探测 hello 消息。

```go
type Peer interface {
    GetPeerEndpoint() (*pb.PeerEndpoint, error)
    NewOpenchainDiscoveryHello() (*pb.Message, error)
}
```

具体实现的数据结构为 PeerImpl。

```go
type PeerImpl struct {
    handlerFactory HandlerFactory  // 生成一个 MessageHandler
    handlerMap     *handlerMap     // 所有注册上来的消息处理器
    ledgerWrapper  *ledgerWrapper  // ledger 操作句柄
    secHelper      crypto.Peer     // 处理身份验证和安全相关
    engine         Engine          // handler 工厂 + 本地交易处理的引擎
    isValidator    bool
    reconnectOnce  sync.Once
    discHelper     discovery.Discovery //探测任务句柄
    discPersist    bool
}
```

核心方法，包括：

* ExecuteTransaction：准备执行一个交易，发送给本地的引擎（VP 节点），或给远端的 peer；
* Broadcast：向所有注册的消息句柄发送消息；
* Unicast：向指定的 peer 发送消息；
* 一系列 Get 方法：包括获取 Block 内容、获取当前链的大小、当前状态的 hash、获取已注册的 peer 端点、远端 ledger 等等，很多功能实际上都是通过其它包来完成。
* sendTransactionsToLocalEngine：交易发给本地的引擎处理；
* SendTransactionsToPeer：交易发给其它 peer 处理；

#### 消息相关

两个基础接口 MessageHandler 和 MessageHandlerCoordinator。

```go
type MessageHandler interface {
    RemoteLedger    // 获取远端的 ledger
    HandleMessage(msg *pb.Message) error // 接收到某个消息进行处理
    SendMessage(msg *pb.Message) error //发送消息到对端
    To() (pb.PeerEndpoint, error) // 对端是哪个节点
    Stop() error
}
```

```go
type MessageHandlerCoordinator interface {
    Peer
    SecurityAccessor
    BlockChainAccessor
    BlockChainModifier
    BlockChainUtil
    StateAccessor
    RegisterHandler(messageHandler MessageHandler) error
    DeregisterHandler(messageHandler MessageHandler) error
    Broadcast(*pb.Message, pb.PeerEndpoint_Type) []error
    Unicast(*pb.Message, *pb.PeerID) error
    GetPeers() (*pb.PeersMessage, error)
    GetRemoteLedger(receiver *pb.PeerID) (RemoteLedger, error)
    PeersDiscovered(*pb.PeersMessage) error
    ExecuteTransaction(transaction *pb.Transaction) *pb.Response
    Discoverer
}
```



