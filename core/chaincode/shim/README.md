### shim

shim 包可以让 chaincode 代码跟 ledger 进行交互。

比较重要的 ChaincodeStub 结构，提供了用户 chaincode 代码跟 ledger 进行交互的接口，例如 PutState 与 GetState 来写入和查询链上键值对的状态。
