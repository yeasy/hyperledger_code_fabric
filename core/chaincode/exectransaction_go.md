### exectransaction.go

提供 `func Execute(ctxt context.Context, cccid *ccprovider.CCContext, spec interface{}) (*pb.Response, *pb.ChaincodeEvent, error) ` 方法。

主要过程：

* 提起 ChaincodeSupport，Launch()（检查链码容器是否已经提起来了，如果没有则提起 chaincode，等待 register状态，转换为ready状态）。
* 通过 ChaincodeSupport 来执行，Execute()（检查链码容器是否运行，handler.sendExecuteMessage()，监听并返回消息 response，类型为ChaincodeMessage）。
* 检查response.Type 是否为 COMPLETED
* 返回解码后的 response.Payload、response.ChaincodeEvent。
