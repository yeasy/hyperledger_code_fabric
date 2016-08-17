## rest

所有的 REST API 相关的方法。

这里是通过 REST 接口来跟 fabric 交互（发起交易等）的入口。

最关键的文件，包括 rest\_api.go 和 api.go，前者提供 REST 服务（ServerOpenchainREST 结构）响应，后者为前者提供一些查询接口（ServerOpenchain 结构）。

一个典型交易的调用过程，如下图所示。

![利用 REST 发起交易的调用过程](../_images/transaction_process.png)

