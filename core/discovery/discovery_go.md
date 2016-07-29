### discovery.go
```go
type Discovery interface {
	AddNode(string) bool           // Add an address to the discovery list
	RemoveNode(string) bool        // Remove an address from the discovery list
	GetAllNodes() []string         // Return all addresses this peer maintains
	GetRandomNodes(n int) []string // Return n random addresses for this peer to connect to
	FindNode(string) bool          // Find a node in the discovery list
}
```

定义发现服务的接口。

实现的数据结构为

```go
type DiscoveryImpl struct {
	sync.RWMutex
	nodes  map[string]bool //节点信息，是地址到 bool 的映射，true 为加入后的节点，false 为删除掉的
	seq    []string  // 保存所有添加过的节点的地址
	random *rand.Rand
}
```
