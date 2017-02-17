## mspimpl.go

对 MSP 接口的实现，实现了 bccspmsp 结构，可以通过 NewBccspMsp() 方法生成。

bccspmsp 提供一个默认的 MSP 实现，成员包括：

* Type 为 `FABRIC = 0`；
* bccsp 为 SHA-2 256；


方法主要包括：

* Setup()：利用给定的配置信息，进行初始化操作。
* GetType()：返回类型，目前为 `FABRIC`（值为 0）。
* GetIdentifier()：返回 msp 的名称。
* GetDefaultSigningIdentity()：获取本 MSP 中的默认签名个体。
* GetSigningIdentity()：获取本 MSP 中的签名个体。
* Validate（）：对给定的 Identity 对象进行校验。
* DeserializeIdentity()：从序列化对象中解析 Identity 对象。
* SatisfiesPrincipal()：检查某个 Identity 是否符合给定的策略。

* getIdentityFromConf()：从本地配置文件中解析出 x.509 格式的证书信息，包括公钥等，利用这些信息生成 Identity 对象。
* getSigningIdentityFromConf()：从本地配置文件中解析出 x.509 格式的证书信息，包括公钥等，利用这些信息生成带签名功能的 Identity 对象。
