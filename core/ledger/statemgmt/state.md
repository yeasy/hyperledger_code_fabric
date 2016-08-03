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

方法TODO。