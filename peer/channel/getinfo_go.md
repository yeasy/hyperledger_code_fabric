### getinfo.go

`peer channel`getinfo命令的入口。

命令处理函数是getinfo，首先通过 InitCmdFactory 进行初始化，然后再调用getBlockChainInfo。

