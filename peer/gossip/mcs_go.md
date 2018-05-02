### mcs.go

mspMessageCryptoService：gossip消息加密服务相关实现，其实现了MessageCryptoService接口。

#### NewMCS

根据参数创建一个mspMessageCryptoService实例。此函数由3个参数：

* policies.ChannelPolicyManagerGetter：通过管理器方法访问给定通道的策略管理器。

* crypto.LocalSigner:签名功能实例

* mgmt.DeserializersManager：反序列化管理器

#### ValidateIdentity

验证远程peer的身份。如果peer身份是invalid, revoked, expired，那么返回错误；否则返回nil

#### GetPKIidOfCert

返回peer身份的PKI-ID，如果出错，返回nil。peer的PKI-ID是作为MSP身份的序列化版本的peerIdentity的SHA2-256计算值。

* 反序列化peerIdentity
* 连接反序列化后的msp-id和 idbytes得到raw字节数组
* 调用Hash对raw进行sha2-256加密，返回hash结果

#### VerifyBlock

验证区块是否已经签名，并且区块头里面的序列号与传入的序列号一致，都一致返回nil，否则返回错误。

* 把传入的signedBlock字节数组转换为common.block结构
* 比较区块头里面的序列号和传入的序列号是否一致
* 取出channelid与传入的channelID比较是否一致
* 验证Header.DataHash等于block.Data的hash值
* 获得签名验证策略，将签名值和区块数据进行验证

#### Sign

对一个字节数据进行签名。

#### Verify



