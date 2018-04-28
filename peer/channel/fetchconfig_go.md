### fetchconfig.go

`peer channel fetch`命令的入口。如peer channel fetch &lt;newest\|oldest\|config\|\(number\)&gt;  \[outputfile\]

命令处理函数是fetch，首先通过 InitCmdFactory 进行初始化，然后根据不同参数查询不同block。

* newest：调用deliverclient中getNewestBlock函数查询
* oldest：调用deliverclient中getOldestBlock函数查询
* config：先调用deliverclient中getNewestBlock函数查询最新区块，再调用GetLastConfigIndexFromBlock获得上一个区块配置index，再调用getSpecifiedBlock获得指定区块
* 数字：调用deliverclient中getSpecifiedBlock函数查询指定区块

如果需要输出到文件，将查询到的区块信息写入文件。

