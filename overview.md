# 整体结构

源代码主要分为如下三部分：

* 源代码：实现 fabric 功能的核心代码，包括 consensus 包、core 包、events 包、membersrvc 包、peer 包、protos 包；
* 源码相关工具：一些辅助代码包，包括管理依赖的 vendor 包，测试包 bddtests、客户端 SDK 等；
* 其它工具：包括文档、安装脚本 scripts、devenv 等。

源代码目前约为 79K 行。

```sh
$ find fabric -name "*.go" | xargs cat | wc -l
165229
$ find fabric/vendor -name "*.go" | xargs cat | wc -l
86590
```

除了些目录外，还包括一些说明文档、安装需求说明、License 信息文件等。