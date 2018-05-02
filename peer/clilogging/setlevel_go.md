### setlevel.go

`peer logging setlevel`命令的入口。如peer `logging`setlevel  &lt;module regular expression&gt; &lt;log level&gt;

命令处理函数是setLevel，首先通过 InitCmdFactory 进行初始化，然后使用grpc调用peer功能。

* 使用grpc client AdminClient.SetModuleLogLevel调用peer设置模块log等级功能



