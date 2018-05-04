### common.go

clilogging通用函数。

#### InitCmdFactory

初始化命令工厂LoggingCmdFactory，关键是初始化了一个adminClient，其实就是一个与peer进行grpc通信的client。

#### checkLoggingCmdParams

校验log cmd的参数。

