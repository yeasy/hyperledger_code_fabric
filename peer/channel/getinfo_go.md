### getinfo.go

`peer channel`getinfo命令的入口。

命令处理函数是fetch，首先通过 InitCmdFactory 进行初始化，然后根据不同参数查询不同block。

