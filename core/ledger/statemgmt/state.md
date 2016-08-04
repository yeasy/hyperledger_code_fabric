#### state

state 包，维护世界状态。

state/state.go 中的 State 数据结构如下，包含状态管理接口、状态增量等。

```go
type State struct {
	stateImpl             statemgmt.HashableState
	stateDelta            *statemgmt.StateDelta
	currentTxStateDelta   *statemgmt.StateDelta
	currentTxUUID         string
	txStateDeltaHash      map[string][]byte
	updateStateImpl       bool
	historyStateDeltaSize uint64
}
```

目前实现了三种结构来组织和管理世界状态：buckettree、trie 和 raw。在新建一个 State 对象时会指定对应结构的 stateImpl。
当前默认使用 buckettree，这种方法模型化了一棵默克尔树，用 bucket 作为默克尔树的叶子节点。同时树的胖瘦在配置中可调，用于匹配不同场景的性能要求。

