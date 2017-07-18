# 整体结构

Hyperledger Fabric 在 1.0 中，架构已经解耦为三部分：

* fabric-peer：主要起到 peer 作用，包括 endorser、committer 两种角色；
* fabric-ca：即原先的 membersrvc，独立成一个新的项目。
* fabric-order：起到 order 作用。

其中，fabric-peer 和 fabric-order 代码暂时都在 fabric 项目中，未来可能进一步拆分。

## 核心代码
fabric 项目中主要包括代码、工具、脚本等部分，核心源代码目前约为 430 个文件，80K 行。

```sh
$ cd fabric
$ find bccsp common core events gossip msp orderer peer protos \
    -not -path "*/vendor/*" \
    -name "*.go"  \
    -not -name "*_test.go" \
    | wc -l
431
$ find bccsp common core events gossip msp orderer peer protos \
    -not -path "*/vendor/*" \
    -name "*.go"  \
    -not -name "*_test.go" \
    | xargs cat | wc -l
80560
```

### 源代码
实现 fabric 功能的核心代码，包括：

* [bccsp](bccsp/README.md) 包：实现对加解密算法和机制的支持。
* [common](common/README.md) 包：一些通用的模块；
* [core](core/README.md) 包：大部分核心实现代码都在本包下。其它包的代码封装上层接口，最终调用本包内代码；
* [events](events/README.md) 包：支持 event 框架；
* [examples](examples/README.md) 包：包括一些示例的 chaincode 代码；
* [gossip](gossip/README.md) 包：实现 gossip 协议；
* [msp](msp/README.md) 包：Member Service Provider 包；
* [order](order/README.md) 包：order 服务相关的入口和框架代码；
* [peer](peer/README.md) 包：peer 的入口和框架代码；
* [protos](protos/README.md) 包：包括各种协议和消息的 protobuf 定义文件和生成的 go 文件。

### 源码相关工具
一些辅助代码包，包括：

* [bddtests](bddtests)：测试包，含有大量 bdd 测试用例；
* [gotools](gotools)：golang 开发相关工具安装；
* [vendor](vendor) 包：管理依赖；

### 安装部署
包括：

* [busybox](busybox)：busybox 环境，精简的 linux；
* [devenv](devenv)：配置开发环境；
* [images](images)：镜像生成模板等。
* [scripts](scripts)：各种安装配置脚本；

### 其它工具
其他工具，包括：

* [docs](docs)：文档，大部分文档都可以 [在线查阅](http://hyperledger-fabric.readthedocs.io)；


## 配置、脚本和文档

除了些目录外，还包括一些说明文档、安装需求说明、License 信息文件等。

### Docker 相关文件
* .baseimage-release：生成 baseimage 时候的版本号。
* .dockerignore：生成 Docker 镜像时忽略一些目录，包括 .git 目录。

### git 相关文件
* .gitattributes：git 代码管理时候的属性文件，带有不同类型文件中换行符的规则，默认都为 linux 格式，即 `\n`。
* .gitignore：git 代码管理时候忽略的文件和目录，包括 build 和 bin 等中间生成路径。
* .gitreview：使用 git review 时候的配置，带有项目的仓库地址信息。
* README.md：项目的说明文件，包括一些有用的链接等。

### travis 相关文件
* .travis.yml：travis 配置文件，目前是使用 golang 1.6 编辑，运行了三种测试：unit-test、behave、node-sdk-unit-tests。

### 其它
* LICENSE：Apache 2 许可文件。
* docker-env.mk：被 Makefile 引用，生成 Docker 镜像时的环节变量。
* Makefile：执行测试、格式检查、安装依赖、生成镜像等操作。
* mkdocs.yml：生成 http://hyperledger-fabric.readthedocs.io 在线文档的配置文件。
* TravisCI_Readme.md

