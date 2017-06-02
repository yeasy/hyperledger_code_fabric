## main.go
主入口文件。

核心代码非常简单。

```go
func main() {
	conf := config.Load()
	initializeLoggingLevel(conf)
	initializeProfilingService(conf)
	grpcServer := initializeGrpcServer(conf)
	initializeLocalMsp(conf)
	signer := localmsp.NewSigner()
	manager := initializeMultiChainManager(conf, signer)
	server := NewServer(manager, signer)
	ab.RegisterAtomicBroadcastServer(grpcServer.Server(), server)
	logger.Info("Beginning to serve requests")
	grpcServer.Start()
}
```

其中，initializeLocalMsp()和initializeMultiChainManager()做了大部分的工作。


### initializeGrpcServer

创建 grpc 的服务器。

```golang
grpcServer, err := comm.NewGRPCServerFromListener(lis, secureConfig)
	if err != nil {
		logger.Fatal("Failed to return new GRPC server:", err)
	}
```

### initializeLocalMsp

根据 orderer.yaml 配置中路径，读取本地的 msp 数据。

```golang
func initializeLocalMsp(conf *config.TopLevel) {
	// Load local MSP
	err := mspmgmt.LoadLocalMsp(conf.General.LocalMSPDir, conf.General.BCCSP, conf.General.LocalMSPID)
	if err != nil { // Handle errors reading the config file
		logger.Fatal("Failed to initialize local MSP:", err)
	}
}
```

随后，是否需要从外部 genesis 区块文件读入数据，还是根据给定配置初始化 genesis 区块结构。

最后是初始化各个账本结构，并注册 server 和 signer 两个组件到 grpc server 上。

```golang
signer := localmsp.NewSigner()

	manager := multichain.NewManagerImpl(lf, consenters, signer)

	server := NewServer(
		manager,
		signer,
	)

	ab.RegisterAtomicBroadcastServer(grpcServer.Server(), server)
	logger.Info("Beginning to serve requests")
	grpcServer.Start()
```

### initializeMultiChainManager
