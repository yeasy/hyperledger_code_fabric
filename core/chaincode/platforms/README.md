### platforms

这里面主要是生成 chaincode 容器镜像时候，进行打包的一些方法类。

目前支持三种类型：car、golang 和 java。

定义了接口 Platform。

```go
type Platform interface { 
        ValidateSpec(spec *pb.ChaincodeSpec) error 
        WritePackage(spec *pb.ChaincodeSpec, tw *tar.Writer) error
      }
```

ValidateSpec 用来对一个 chaincodeSpec 进行检查；WritePackage 根据一个 spec 来生成一个 tar 包，是对应的容器镜像内容。

三种类型分别都实现了这两个方法。

