### policy.go

主要定义了三个策略相关接口和实现了这三个接口的结构。

* ChannelPolicyManagerGetter：获取指定通道的策略管理器。
* Manager：一个通道相关策略的管理器。
* Proposer：负责处理策略更新的相关操作。

ManagerImpl 结构实现了这三个接口，成为策略相关操作的主要入口结构。

```go
type ManagerImpl struct {
	parent        *ManagerImpl
	basePath      string
	fqPrefix      string
	providers     map[int32]Provider
	config        *policyConfig
	pendingConfig map[interface{}]*policyConfig
	pendingLock   sync.RWMutex

	// SuppressSanityLogMessages when set to true will prevent the sanity checking log
	// messages.  Useful for novel cases like channel templates
	SuppressSanityLogMessages bool
}
```