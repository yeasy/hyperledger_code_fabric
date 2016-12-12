# gotools
go 相关的开发工具的安装脚本：golint、govendor、goimports、protoc-gen-go、ginkgo、gocov、gocov-xml 等。

* golint：支持 golang 的静态语法和格式检查；
* govendor：管理第三方引入包；
* goimports：格式化 import 的包；
* protoc-gen-go：对 protobuf 的支持；
* ginkgo：支持 BDD 的框架；
* gocov：golang 的单元测试覆盖率检查工具；
* gocov-xml：支持 gocov 生成 xml 格式的报告。

在该包下执行 `make` 或在上层执行 make gotools 会自动安装上述工具。