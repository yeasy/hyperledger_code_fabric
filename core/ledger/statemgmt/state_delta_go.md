#### state_delta.go

StateDelta 对象记录 state 的增量变化，用于暂存交易执行时还未提交的状态改变，或用于向其他节点传递状态。

```go
type StateDelta struct {
	ChaincodeStateDeltas map[string]*ChaincodeStateDelta
	// RollBackwards allows one to contol whether this delta will roll the state
	// forwards or backwards.
	RollBackwards bool
}
```

其中，ChaincodeStateDelta 维护一个链码的状态更新，结构如下：

```go
type ChaincodeStateDelta struct {
	ChaincodeID string
	UpdatedKVs  map[string]*UpdatedValue
}

// UpdatedValue holds the value for a key
type UpdatedValue struct {
	Value         []byte
	PreviousValue []byte
}
```

StateDelta 的方法包括对状态增量的增删改查、对 StateDelta 整体计算加密哈希和 Marshal / Unmarshal 等。