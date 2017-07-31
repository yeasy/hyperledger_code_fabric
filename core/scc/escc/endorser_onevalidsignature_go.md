#### endorser_onevalidsignature.go

Invoke\(\)用来背书特定的Proposal。

目前，只能对输入进行签名并返回背书的结果。以后会对chaincode进行扩展，来提供更复杂的背书策略过程。例如把策略详细信息编码成链码的调用交易，以及允许客户通过参数去选择使用哪一个背书策略。

调用ESCC时有4个必须的参数，还有两个可选。

| 序号 | 内容 | 备注 |
| --- | --- | --- |
| 0 | 函数名 | 目前未使用 |
| 1 | 序列化的Header | 必需 |
| 2 | 序列化的ChaincodeProposalPayload | 必需 |
| 3 | 被调用的链码的ChaincodeID | 必需 |
| 4 | 链码调用的结果（Response） | 必需 |
| 5 | 模拟结果（读写集）| 必需 |
| 6 | 序列化的事件 | 非必需 |
| 7 | 可见度 | 非必需，目前是完全可见 |

主要过程如下：

* 检查各个参数。
* 获取该节点的签名身份。
* 根据传进来的参数创建ProposalResponse。
* 将ProposalResponse编码为prBytes，返回shim.Success(prBytes)。
