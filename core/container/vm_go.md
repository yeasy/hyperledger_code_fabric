### vm.go

主要数据结构为

```go
type VM struct {
	Client *docker.Client
}
```

一个 VM，实际代表的是一个容器主机，目前支持列出镜像（用来测试）、生成 chaincode 容器等方法。

主要的方法是 buildChaincodeContainerUsingDockerfilePackageBytes()，根据传入的字节来生成一个 chaincode 镜像。