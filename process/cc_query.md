## 链码查询

主要过程如下。

* 根据传入的各种参数，最终构造签名提案，通过 endorserClient 发送给指定的 Peer。
* 成功的话，获取到 ProposalResponse，打印出 proposalResp.Response.Payload 内容。

需要注意 invoke 和 query 的区别，query 不需要创建 SignedTx 发送到 Orderer，而且会返回查询的结果。