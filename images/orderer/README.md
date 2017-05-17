## orderer

生成 fabric-order 镜像。

包括 Dockerfile.in 文件，内容为：

```sh
FROM hyperledger/fabric-runtime:_TAG_
ENV ORDERER_CFG_PATH /etc/hyperledger/fabric
RUN mkdir -p /var/hyperledger/db /etc/hyperledger/fabric
COPY payload/orderer /usr/local/bin
COPY payload/orderer.yaml $ORDERER_CFG_PATH
EXPOSE 7050
CMD orderer
```