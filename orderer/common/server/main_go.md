#### main.go

orderer 启动后的 main 方法会调用到这里的 `Main()` 方法。

核心代码非常简单。


```go
// Main is the entry point of orderer process
func Main() {
	fullCmd := kingpin.MustParse(app.Parse(os.Args[1:]))

	// "version" command
	if fullCmd == version.FullCommand() {
		fmt.Println(metadata.GetVersionInfo())
		return
	}

	conf := config.Load()
	initializeLoggingLevel(conf)
	initializeLocalMsp(conf)

	Start(fullCmd, conf)
}
```

整体的调用流程如下图所示。

![orderer.common.server 包中的 Main() 方法](../../_images/orderer_common_server_Start.png)


其中，initializeLocalMsp() 和 Start()做了大部分的工作。


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

初始化一个 multichain.Manager 结构。十分核心的代码。

通过 createLedgerFactory() 初始化账本结构，包括 file、json、ram 等类型。file 的话会在本地指定目录（/var/production/chains）下创建账本结构。账本所关联的链名称会自动命名为 chain_FILENAME。之后通过指定初始区块，或自动生成来初始化区块链结构。至此，账本结构初始化完成。

接下来，初始化 consenter 部分，初始化 solo、kafka 两种类型。

账本、consenter，再加上传入的签名者结构，通过 NewManagerImpl() 方法构造一个 multichain.Manager 结构，负责处理消息。并且会挨个检查区块链结构，调用 start 方法启动。

### NewServer

新建一个 Server 结构，包括一个 broadcast 的处理句柄，以及一个 deliver 的处理句柄。

```go
type server struct {
	bh broadcast.Handler
	dh deliver.Handler
}
```

NewServer 分别初始化这两个句柄，挂载上前面初始化的 multichain.Manager 结构。broadcast 句柄还需要初始化一个配置更新的处理器，负责处理 CONFIG_UPDATE 交易。

```go
func NewServer(ml multichain.Manager, signer crypto.LocalSigner) ab.AtomicBroadcastServer {
	s := &server{
		dh: deliver.NewHandlerImpl(deliverSupport{Manager: ml}),
		bh: broadcast.NewHandlerImpl(broadcastSupport{
			Manager:               ml,
			ConfigUpdateProcessor: configupdate.New(ml.SystemChannelID(), configUpdateSupport{Manager: ml}, signer),
		}),
	}
	return s
}
```






















