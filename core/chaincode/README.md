## chaincode

chaincode 相关，包括生成 chaincode 镜像，支持对 chaincode 的调用、查询等。

比较核心的结构包括：

* ChaincodeSupport：通过调用 vmc 驱动来支持对 chaincode 容器的管理，包括部署、执行合约等；
* Handler：通过一个状态机来维护 peer 侧对于 chaincode 各种状态下的响应，before、after 等触发条件；

此外，shim 包负责提供 Chaincode 跟账本结构打交道的中间层。

* ChaincodeStub：chaincode 中代码通过该结构提供的方法来修改账本状态；
* Handler：chaincode 一侧用状态机来跟踪 shim 相关事件。

platforms 包提供具体的对 Chaincode 运行类型的支持，包括 golang、java、car 等。例如在部署的时候完成打包任务。