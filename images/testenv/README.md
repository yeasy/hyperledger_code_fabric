## testenv
生成一个测试环境镜像。

包括 Dockerfile.in 文件，内容为：

```sh
FROM hyperledger/fabric-baseimage:_BASE_TAG_
ADD payload/gotools.tar.bz2 /usr/local/bin/
WORKDIR /opt/gopath/src/github.com/hyperledger/fabric
```