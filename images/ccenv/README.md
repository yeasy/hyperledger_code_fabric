## ccenv

生成 chaincode 运行的环境镜像。

包括 Dockerfile.in 文件，内容为：

```sh
FROM hyperledger/fabric-baseimage:_BASE_TAG_
COPY payload/chaintool payload/protoc-gen-go /usr/local/bin/
ADD payload/goshim.tar.bz2 $GOPATH/src/
```
