### install.go
响应 `peer chaincode install` 命令。

调用 chaincodeInstall 方法。

一种是通用的方式，通过传入的参数进行打包；一种是直接读入传入的打包文件 ccpackfile 进行处理。

首先将 chaincode 相关数据生成一个 ChaincodeDeploymentSpec（CDS）结构，然后进行签名等，最后通过 install 方法，转化为一个 protobuf 消息，发送给 peer。

#### 生成 ChaincodeDeploymentSpec 结构

![ChaincodeDeploymentSpec 结构](../_images/proto-peer-chaincode.png)

其中，ChaincodeDeploymentSpec 结构的 CodePackage 变量包括所调用的 chaincode 的代码和所需要的环境代码（例如整个 $GOPATH/src 目录下数据），为 tar 格式的二进制数据。

#### 进行 endorsement
安装之前，根据 CDS 来生成 Install Proposal
