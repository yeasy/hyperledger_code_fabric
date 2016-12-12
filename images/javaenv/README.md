## javaenv

生成 java chaincode 运行的环境镜像。

包括 Dockerfile.in 文件，内容为：

```sh
FROM hyperledger/fabric-baseimage:_BASE_TAG_
RUN wget https://services.gradle.org/distributions/gradle-2.12-bin.zip -P /tmp --quiet
RUN unzip -q /tmp/gradle-2.12-bin.zip -d /opt && rm /tmp/gradle-2.12-bin.zip
RUN ln -s /opt/gradle-2.12/bin/gradle /usr/bin
ADD payload/javashim.tar.bz2 /root
ADD payload/protos.tar.bz2 /root
ADD payload/settings.gradle /root
WORKDIR /root
# Build java shim after copying proto files from fabric/proto
RUN core/chaincode/shim/java/javabuild.sh
```