### pbft.go

主要方法是 New()，返回一个 Consenter 的实现结构。

目前 pbft 只支持 batch 模式。

```go
func New(stack consensus.Stack) consensus.Consenter {
	handle, _, _ := stack.GetNetworkHandles()
	id, _ := getValidatorID(handle)

	switch strings.ToLower(config.GetString("general.mode")) {
	case "batch":
		return newObcBatch(id, config, stack)
	default:
		panic(fmt.Errorf("Invalid PBFT mode: %s", config.GetString("general.mode")))
	}
}
```


![consensus pbft 主要过程](../_images/consensus_pbft.png)
