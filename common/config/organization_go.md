### organization.go


包括几个重要的数据结构和它们的操作方法。

* OrganizationProtos：定义一个 MSP 配置，包括这个 MSP 的证书、签名、验证等需要的信息。实际上就代表了一个 MSP 的完整信息。
* OrganizationConfig：代表组织的配置，不仅包括 MSP 信息，还包括配置组的引用。
* OrganizationGroup：组合自


```go
type OrganizationProtos struct {
	MSP *mspprotos.MSPConfig
}

type OrganizationConfig struct {
	*standardValues
	protos *OrganizationProtos

	organizationGroup *OrganizationGroup

	msp   msp.MSP
	mspID string
}

// Config stores common configuration information for organizations
type OrganizationGroup struct {
	*Proposer
	*OrganizationConfig
	name             string
	mspConfigHandler *mspconfig.MSPConfigHandler
}
```