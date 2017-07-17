### chaincodeexec.go

根据链码名和参数去执行链码，调用ExecuteChaincode\(\)。
从LSCC获取链码部署详情ChaincodeDeploymentSpec和链码数据ChaincodeData也是通过调用ExecuteChaincode\(\)来实现。

#### ExecuteChaincode

* 创建简单的ChaincodeInvocationSpec，只包含链码名字和Args。
* 核心：调用Execute\(\)（见core/chaincode/exectransaction.go），返回响应response和事件event。
* 返回response、event。
