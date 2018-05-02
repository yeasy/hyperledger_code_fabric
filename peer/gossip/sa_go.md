### sa.go

mspSecurityAdvisor：使用peer MSP实现SecurityAdvisor接口。

#### NewSecurityAdvisor

创建一个mspSecurityAdvisor实例。

#### OrgByPeerIdentity

返回peerIdentity的组织的身份OrgIdentityType。

* 先与本地msp进行比对，获得本地反序列化实例GetLocalDeserializer，对peerIdentity反序列化。如果没有报错，则认为local MSP成功反序列化了，调用GetMSPIdentifier返回OrgIdentityType。

* 上面一步没有校验成功，则调用GetChannelDeserializers获得chainID和对应的IdentityDeserializer，然后使用反序列化实例deserializer进行peerIdentity反序列化，判断反序列化是否成功，成功则调用GetMSPIdentifier返回OrgIdentityType。



