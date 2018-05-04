### getlevel.go

`peer logging getlevel`命令的入口。如peer `logging getlevel`&lt;module&gt;

命令处理函数是getLevel，首先通过 InitCmdFactory 进行初始化，然后向peer查询。

* 使用grpc client AdminClient.GetModuleLogLevel向peer查询模块log等级

将查询到的logResponse输出到logger。

