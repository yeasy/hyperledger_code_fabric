### exectransaction.go

Execute\(\)

主要过程：（核心见chaincode_support.go中的Launch\(\)和Execute\(\)）

* 提起ChaincodeSupport，Launch\(\)（检查链码容器是否已经提起来了，如果没有则提起chaincode，等待register状态，转换为ready状态）。
* 通过ChaincodeSupport来执行，Execute\(\)（检查链码容器是否运行，handler.sendExecuteMessage\(\)，监听并返回消息response，类型为ChaincodeMessage）。
* 检查response.Type是否为COMPLETED
* 返回解码后的response.Payload、response.ChaincodeEvent。
