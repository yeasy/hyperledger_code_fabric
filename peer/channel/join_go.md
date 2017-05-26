### join.go

join 过程也十分简单，主要是 peer 完成。

* 客户端创建一个 ChaincodeSpec 结构，其 input 中调用方式是 CSCC.JoinChain，参数为所加入通道的初始区块。
* 利用 CS 构造一个 ChaincodeInvocationSpec 结构。
* 利用 CIS，创建 Proposal 结构并进行签名，头部类型为 CONFIG。