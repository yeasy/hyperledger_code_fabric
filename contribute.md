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

