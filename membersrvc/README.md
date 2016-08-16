# membersrvc
成员管理功能模块。

Membersrvc 启动后作为一个 CA 来发挥自己的作用。

公众用户通过验证 CA 的签字从而信任 CA ，任何人都可以得到 CA 的证书（含公钥），用以验证它所签发的证书。

如果用户想得到一份属于自己的证书，他应先向 CA 提出申请。在 CA 判明申请者的身份后，便为他分配一个公钥，并且 CA 将该公钥与申请者的身份信息绑在一起，并为之签字后，便形成证书发给申请者。

如果一个用户想鉴别另一个证书的真伪，他就用 CA 的公钥对那个证书上的签字进行验证，一旦验证通过，该证书就被认为是有效的。证书实际是由证书签证机关（CA）签发的对用户的公钥的认证。

在 Hyperledger 中，将会创建四种 CA 服务，分别是：Attribute Certificate Authority、Enrollment Certificate Authority、Transaction Certificate Authority、TLS Certificate Authority。

- ACA(Attribute Certificate Authority)：
在 Tcret 创建过程中，为了验证用户之间传递的特征值（Attributes ）正确，引入了 Attribute Certificate Authority。并且对于有效特征值，会返回相应的 ACert。
- ECA(Enrollment Certificate Authority)：
ECA 允许型用户注册到 blockchain 网络中，并且使每一位注册用户得到相应的 Ecert 证书。
- TCA(Transaction Certificate Authority)：
一旦一个用户加入到网络中，它能够向 TCA 机构请求获得相应的交易证书（transaction certificates）。该证书可以用在对智能合约进行 deploy 和 invoke 相应的操作。
- TLSCA(TLS Certificate Authority)：
除了 Ecert 和 Tcert，用户需要 TLS cert 来保护他们的交易信息。Tls cert 可以从 TLS certificate authority (TLSCA) 获取。
