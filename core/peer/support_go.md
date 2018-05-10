### support.go

peer support接口和工厂的实现，提供访问peer资源的一些方法。

```go
// Support gives access to peer resources and avoids calls to static methods
type Support interface {
	// GetApplicationConfig returns the configtxapplication.SharedConfig for the channel
	// and whether the Application config exists
	GetApplicationConfig(cid string) (channelconfig.Application, bool)

	// ChaincodeByName returns the definition (and whether they exist)
	// for a chaincode in a specific channel
	ChaincodeByName(chainname, ccname string) (resourcesconfig.ChaincodeDefinition, bool)
}
```

#### supportImpl

实现support接口

* GetApplicationConfig返回指定channel的配置信息

* ChaincodeByName返回指定chaincode定义



```go
// SupportFactory is a factory of Support interfaces
type SupportFactory interface {
	// NewSupport returns a Support interface
	NewSupport() Support
}
```

#### SupportFactoryImpl

实现supportfactory工厂，用来创建support实例。

