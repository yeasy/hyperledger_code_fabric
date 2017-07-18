### applicationorg.go

主要提供三个重要的数据结构，代表应用组织。

```go
type ApplicationOrgProtos struct {
	AnchorPeers *pb.AnchorPeers
}

type ApplicationOrgConfig struct {
	*OrganizationConfig
	protos *ApplicationOrgProtos

	applicationOrgGroup *ApplicationOrgGroup
}

// ApplicationOrgGroup defines the configuration for an application org
type ApplicationOrgGroup struct {
	*Proposer
	*OrganizationGroup
	*ApplicationOrgConfig
}
```