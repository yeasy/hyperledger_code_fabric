### shim

shim 包可以让 chaincode 代码跟 ledger 进行交互。

比较重要的 ChaincodeStub 结构，提供了用户 chaincode 代码跟 ledger 进行交互的一系列方法，例如 PutState 与 GetState 来写入和查询链上键值对的状态。


用户链码 --> ChaincodeStub --> Handler -----ChaincodeMessage -----> Peer