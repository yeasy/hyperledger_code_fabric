## devops.proto

跟 Chaincode 的创建、部署、调用、查询等相关的一些操作接口。

```
service Devops {
    // Log in - passed Secret object and returns Response object, where
    // msg is the security context to be used in subsequent invocations
    rpc Login(Secret) returns (Response) {}

    // Build the chaincode package.
    rpc Build(ChaincodeSpec) returns (ChaincodeDeploymentSpec) {}

    // Deploy the chaincode package to the chain.
    rpc Deploy(ChaincodeSpec) returns (ChaincodeDeploymentSpec) {}

    // Invoke chaincode.
    rpc Invoke(ChaincodeInvocationSpec) returns (Response) {}

    // Invoke chaincode.
    rpc Query(ChaincodeInvocationSpec) returns (Response) {}

    // Retrieve a TCert.
    rpc EXP_GetApplicationTCert(Secret) returns (Response) {}

    // Prepare for performing a TX, which will return a binding that can later be used to sign and then execute a transaction.
    rpc EXP_PrepareForTx(Secret) returns (Response) {}

    // Prepare for performing a TX, which will return a binding that can later be used to sign and then execute a transaction.
    rpc EXP_ProduceSigma(SigmaInput) returns (Response) {}

    // Execute a transaction with a specific binding
    rpc EXP_ExecuteWithBinding(ExecuteWithBinding) returns (Response) {}

}
```