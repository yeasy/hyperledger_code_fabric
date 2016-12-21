## main.go

peer 服务是主服务。

该服务支持各种 peer 命令。

包括查询状态，和启动、停止节点服务等。

各个子命令在对应子包中，如

* node：node 对应子命令
* chaincode：chaincode 对应子命令

