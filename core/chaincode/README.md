## chaincode

chaincode 相关，包括生成 chaincode 镜像，支持对 chaincode 的调用、查询等。

包内代码分两部分，根目录下是 Peer 侧代码，比较核心的结构包括：

* ChaincodeSupport：通过调用 vmc 驱动来支持对 chaincode 容器的管理，包括启动容器、注册、执行合约等；
* Handler：cc 容器启动后，会发送注册消息让 peer 创建一个 handler 结构，并进入主循环响应消息。peer 侧通过一个状态机来维护对于 chaincode 各种消息的响应，利用 before、after 等触发条件。

下面 shim 包则提供 Chaincode 跟账本结构打交道的中间层。

* ChaincodeStub：chaincode 中代码通过该结构提供的方法来修改账本状态；
* Handler：chaincode 一侧用状态机来跟踪 shim 相关事件。

platforms 包提供具体的对 Chaincode 运行类型的支持，包括 golang、java、car 等。例如在部署的时候完成打包任务。


![](../_images/chaincode_msg.png)