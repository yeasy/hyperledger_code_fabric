### cauthdsl_builder.go
定义一些常见的策略。

包括：

* AcceptAllPolicy
* RejectAllPolicy

另外，提供了一些常用的构造方法，包括

* Envelope(policy *cb.SignaturePolicy, identities [][]byte) *cb.SignaturePolicyEnvelope：快速构造一个策略信封结构。

一些策略构造方法：
* SignedBy(index int32) *cb.SignaturePolicy：构造一条指定签名者的签名策略。
* SignedByMspMember(mspId string) *cb.SignaturePolicyEnvelope：指定一条指定签名身份为某 MSP 成员（至少一个）的签名策略。
* SignedByMspAdmin(mspId string) *cb.SignaturePolicyEnvelope：指定一条指定签名身份为某 MSP 管理员（至少一个）的签名策略。
* SignedByAnyMember(ids []string) *cb.SignaturePolicyEnvelope：被给定组织列表中任意成员签名。
* SignedByAnyAdmin(ids []string) *cb.SignaturePolicyEnvelope：被给定组织列表中任意管理员签名。

一些策略的组合逻辑（与、或、集合中的若干个）方法：
* And(lhs, rhs *cb.SignaturePolicy) *cb.SignaturePolicy：两个策略的与组合。
* Or(lhs, rhs *cb.SignaturePolicy) *cb.SignaturePolicy：两个策略的或组合。
* NOutOf(n int32, policies []*cb.SignaturePolicy) *cb.SignaturePolicy：集合中的至少若干个。