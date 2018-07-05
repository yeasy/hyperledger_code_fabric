### metadata.go

定义两个重要结构体和相关方法。


```go
// InstalledChaincode defines metadata about an installed chaincode
type InstalledChaincode struct {
	Name    string
	Version string
	Id      []byte
}

// Metadata defines channel-scoped metadata of a chaincode
type Metadata struct {
	Name              string
	Version           string
	Policy            []byte
	Id                []byte
	CollectionsConfig []byte
}
```