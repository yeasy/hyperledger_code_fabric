#### dockercontroller.go

抽象一个 Docker 主机。

主要结构为

```go
type DockerVM struct {
	id string
}
```

支持的方法包括：
* Deploy：利用给定的 tar.gz 文件生成一个镜像；
* Destroy：删除一个镜像。
* Start：启动一个 Docker 容器，命名为 网络 id + peer id + chaincode 名（hash 串），会从配置中读取 hostconfig 信息。
* Stop：停止一个 Docker 容器。