### proputils.go


#### CreateChaincodeProposal

* 节点身份证书序列化后与随机数计算出交易ID。
* 构建ChaincodeHeaderExtension（含有ChaincodeID）。
* 构建ChaincodeProposalPayload（含有ChaincodeSpec）。
* 获取事件戳。
* 构建Header。
* 返回Proposal。
