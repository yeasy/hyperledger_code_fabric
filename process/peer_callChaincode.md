


#### callChaincode 方法

主要过程如下：

* 判断交易模拟器，不为空则把它加入到Context的K-V存储中。
* 判断被call的cc是不是系统链码，创建CCContext（包含通道名、链码名、版本号、交易ID、是否 SCC、签名 Prop、Prop）
* 调用 core/chaincode/chaincodeexec.go 下的 ExecuteChaincode()，返回响应 response 和 事件ccevent。
* 返回 response和ccevent。