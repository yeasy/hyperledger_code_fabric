### platforms

这里面主要是生成 chaincode 容器镜像时候，进行打包的一些方法类。

Fabric v1.1支持以下几种类型：car、golang、node、java。

定义了接口 Platform。

```go
type Platform interface {
	ValidateSpec(spec *pb.ChaincodeSpec) error
	ValidateDeploymentSpec(spec *pb.ChaincodeDeploymentSpec) error
	GetDeploymentPayload(spec *pb.ChaincodeSpec) ([]byte, error)
	GenerateDockerfile(spec *pb.ChaincodeDeploymentSpec) (string, error)
	GenerateDockerBuild(spec *pb.ChaincodeDeploymentSpec, tw *tar.Writer) error
}
```

ValidateSpec 用来对一个 chaincodeSpec 进行检查；
ValidateDeploymentSpec 用来检查部署时(对应sdk的Client.install，lscc的install Transaction)的deploymentSpec, 这个deploymentSpec是由SDK构造并传入的
GetDeploymentPayload 按照部署时的chaincodePath，将chaincodePath目录下的所有文件打包到一个tar包里
GenerateDockerfile 根据chaincode的deploymentSpec，确定chaincode使用的runtime（go和node使用hyperledger/fabric-ccenv, java使用hyperledger/fabric-javaenv）,构造build dockerimage的Dockerfile
GenerateDockerBuild 编译chaincode，（java 对应的是gradle build， node对应的是npm install）

所有的类型都实现了Platform接口
