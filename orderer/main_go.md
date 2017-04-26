## main.go
主入口文件。

主要过程为：

创建 grpc 的服务器。

```golang
// Create GRPC server - return if an error occurs
	grpcServer, err := comm.NewGRPCServerFromListener(lis, secureConfig)
	if err != nil {
		logger.Error("Failed to return new GRPC server:", err)
		return
	}
```

读取本地的 msp 配置。

```golang
err = mspmgmt.LoadLocalMsp(conf.General.LocalMSPDir, conf.General.BCCSP, conf.General.LocalMSPID)
	if err != nil { // Handle errors reading the config file
		logger.Panic("Failed to initialize local MSP:", err)
	}
```

随后，是否需要从外部 genesis 区块文件读入数据，还是根据给定配置初始化 genesis 区块结构。