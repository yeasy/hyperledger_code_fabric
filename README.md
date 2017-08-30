# Hyperledger 源码分析之 Fabric

0.5.9

区块链技术是计算机技术与金融技术交融的成功创新，被认为是极具潜力的分布式账本平台的核心技术。如果你还不了解区块链，可以阅读 [区块链技术指南](https://www.gitbook.com/book/yeasy/blockchain_guide)。

作为 Linux 基金会支持的开源分布式账本平台，[Hyperledger](https://hyperledger.org) 受到了众多企业的支持和开源界的关注。本书将试图剖析 Hyperledger Fabric 项目相关源码，帮助大家深入理解其实现原理。

在线阅读：[GitBook](https://www.gitbook.com/book/yeasy/hyperledger_code_fabric) 或 [GitHub](https://github.com/yeasy/hyperledger_code_fabric/blob/master/SUMMARY.md)。

欢迎大家加入 [区块链技术讨论群](https://www.gitbook.com/book/yeasy/blockchain_guide)。

## 版本历史
* 0.6.0: 2017-XX-YY

  * configtxgen 模块；
  * cryptogen 模块；
  * 根据 fabric 1.0. 调整结构。
  * 根据 fabric 1.0.1 调整结构。

* 0.5.0: 2017-05-08

  * 完成 peer 模块；
  * 按照最新代码结构更新；
  * 完成部分 orderer 模块分析。

* 0.4.0: 2016-12-12

  * 完成 0.6 分支；
  * 开始 1.0 分支新架构代码。

* 0.3.0: 2016-08-04

  * 完成主要模块内容。

* 0.2.0: 2016-07-01

  * 基本功能分析。

* 0.1.0: 2016-06-08

  * 完成基础框架。

## 参与贡献

贡献者 [名单](https://github.com/yeasy/hyperledger_code_fabric/graphs/contributors)。

本书源码开源托管在 Github 上，欢迎参与维护：[github.com/yeasy/hyperledger\_code\_fabric](https://github.com/yeasy/hyperledger_code_fabric)。

首先，在 GitHub 上 `fork` 到自己的仓库，如 `docker_user/hyperledger_code_fabric`，然后 `clone` 到本地，并设置用户信息。

```sh
$ git clone git@github.com:docker_user/hyperledger_code_fabric.git
$ cd hyperledger_code_fabric
$ git config user.name "yourname"
$ git config user.email "your email"
```

更新内容后提交，并推送到自己的仓库。

```sh
$ #do some change on the content
$ git commit -am "Fix issue #1: change helo to hello"
$ git push
```

最后，在 GitHub 网站上提交 pull request 即可。

另外，建议定期使用项目仓库内容更新自己仓库内容。

```sh
$ git remote add upstream https://github.com/yeasy/hyperledger_code_fabric
$ git fetch upstream
$ git checkout master
$ git rebase upstream/master
$ git push -f origin master
```

_本书结构由 _[_gitbook\_gen_](https://github.com/yeasy/code_snippet/blob/master/python/gitbook_gen.py)_ 维护_。

