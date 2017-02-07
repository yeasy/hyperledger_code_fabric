# images

一些跟 Docker 镜像生成相关的配置和脚本。主要包括各个镜像的 Dockerfile.in 文件。这些文件是生成 Dockerfile 的模板。

主要包括如下镜像的 Dockerfile 的模板和生成相关脚本：

* ccenv：golang chaincode 运行的基础环境，包括 chaintool、protoc-gen-go 和 goshim 包。
* javaenv：运行 java chaincode 的基础环境，包括 javashim、protos、maven、java 环境等。
* kafka：kafka 镜像。
* order：fabric-orderer 镜像，包括 orderer 二进制文件、orderer.yaml 配置文件、msp-sampleconfig 包等。
* peer：fabric-peer 镜像，包括 peer 二进制文件、core.yaml 配置文件、msp-sampleconfig、genesis-sampleconfig 包等。
* tetenv：测试环境镜像，包括 gotools、peer、orderer 等二进制和配置文件、msp-sampleconfig、softhsm2。
* zookeeper：zookeeper 镜像。



