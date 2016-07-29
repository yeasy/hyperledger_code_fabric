### handler.go

对 peer 收到的各种消息进行处理，包括 DISC_HELLO、DISC_GET_PEERS、DISC_PEERS、SYNC_BLOCK_ADDED、SYNC_GET_BLOCKS、SYNC_BLOCKS、SYNC_STATE_GET_SNAPSHOT、SYNC_STATE_SNAPSHOT、STATE_GET_DELTAS、STATE_DELTAS。

#### 核心结构和方法

核心的数据结构为 handler，对消息处理的主结构体。

```go
// Handler peer handler implementation.
type Handler struct {
	chatMutex                     sync.Mutex
	ToPeerEndpoint                *pb.PeerEndpoint
	Coordinator                   MessageHandlerCoordinator
	ChatStream                    ChatStream
	doneChan                      chan struct{}
	FSM                           *fsm.FSM
	initiatedStream               bool // Was the stream initiated within this Peer
	registered                    bool
	syncBlocks                    chan *pb.SyncBlocks
	snapshotRequestHandler        *syncStateSnapshotRequestHandler //处理收到的快照信息
	syncStateDeltasRequestHandler *syncStateDeltasHandler //处理同步过来的状态差异
	syncBlocksRequestHandler      *syncBlocksRequestHandler  //处理同步过来的块
}
```

包对外暴露的主要方法是 

```go
func NewPeerHandler(coord MessageHandlerCoordinator, stream ChatStream, initiatedStream bool, nextHandler MessageHandler) (MessageHandler, error) 
```

返回一个创建好的消息处理器。由有限状态机初始化处理流程，注册各种消息处理的方法，最后发送一个 DISC_HELLO 消息。

#### DISC_HELLO
收到 hello 消息，会将节点信息存储到本地，然后根据需要返回 hello 消息。

#### DISC_GET_PEERS
返回 DISC_PEERS 消息，将本地看到的节点信息发回去。

#### DISC_PEERS
收到别人发来的 peer list，进行探测。

#### SYNC_BLOCK_ADDED


#### SYNC_GET_BLOCKS
发送 block 信息，目前实现是将范围内的 block 挨个封装为 SYNC_BLOCKS 消息发出去。

#### SYNC_BLOCKS

#### SYNC_STATE_GET_SNAPSHOT

#### SYNC_STATE_SNAPSHOT

#### STATE_GET_DELTAS

#### STATE_DELTAS