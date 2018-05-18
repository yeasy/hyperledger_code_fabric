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

![orderer.common.server 包中的 Main\(\) 方法](../../_images/orderer_common_server_Start.png)

其中：

* config.Load\(\)：从本地配置文件和环境变量中读取配置信息，构建配置树结构。
* initializeLoggingLevel\(conf\)：配置日志级别。
* initializeLocalMsp\(conf\)：配置 MSP 结构。
* Start\(\)：完成启动后的核心工作。

##### initializeLocalMsp

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

##### Start\(\)

Start\(\) 完成了 Orderer 节点主要的启动过程。

首先，利用 localmsp，创建签名者结构。

接下来是初始化各个账本结构。如果本地不存在旧的账本文件时，需要初始化系统通道，判断是否需要从外部 genesis 区块文件读入数据，还是根据给定配置初始化 genesis 区块结构。

接下来，新建 grpc server，其中包括 Broadcast\(\) 和 Deliver\(\) 两个服务接口。

最后，启动 grpc 服务。

```golang
func Start(cmd string, conf *config.TopLevel) {
    signer := localmsp.NewSigner()
    manager := initializeMultichannelRegistrar(conf, signer)
    server := NewServer(manager, signer, &conf.Debug)

    switch cmd {
    case start.FullCommand(): // "start" command
        logger.Infof("Starting %s", metadata.GetVersionInfo())
        initializeProfilingService(conf)
        grpcServer := initializeGrpcServer(conf)
        ab.RegisterAtomicBroadcastServer(grpcServer.Server(), server)
        logger.Info("Beginning to serve requests")
        grpcServer.Start()
    case benchmark.FullCommand(): // "benchmark" command
        logger.Info("Starting orderer in benchmark mode")
        benchmarkServer := performance.GetBenchmarkServer()
        benchmarkServer.RegisterService(server)
        benchmarkServer.Start()
    }
}
```

##### initializeGrpcServer

创建 grpc 的服务器。

```golang
grpcServer, err := comm.NewGRPCServerFromListener(lis, secureConfig)
if err != nil {
logger.Fatal("Failed to return new GRPC server:", err)
}
```

### initializeMultichannelRegistrar

这一部分是十分核心的初始化步骤。

初始化一个 multichannel.Registrar 结构，是整个服务的访问和控制核心结构。

```go
type Registrar struct {
	chains          map[string]*ChainSupport
	consenters      map[string]consensus.Consenter
	ledgerFactory   blockledger.Factory
	signer          crypto.LocalSigner
	systemChannelID string
	systemChannel   *ChainSupport
	templator       msgprocessor.ChannelConfigTemplator
	callbacks       []func(bundle *channelconfig.Bundle)
}
```

通过 createLedgerFactory\(\) 初始化账本结构，包括 file、json、ram 等类型。file 的话会在本地指定目录（/var/production/chains）下创建账本结构。账本所关联的链名称会自动命名为 chain\_FILENAME。之后通过指定初始区块，或自动生成来初始化区块链结构。至此，账本结构初始化完成。

接下来，初始化 consenter 部分，初始化 solo、kafka 两种类型。

账本、consenter，再加上传入的签名者结构，通过 multichannel.NewRegistrar\(\) 方法构造一个 multichannel.Registrar 结构，负责处理消息。

multichannel.NewRegistrar\(\) 方法会查看本地已有的链结构文件（例如重启后），并挨个执行如下过程：

* 创建账本工厂对象；
* 获取配置区块，根据配置区块创建账本资源对象；
* 如果配置中包括联盟信息，说明该链结构是系统链，调用。
* 如果配置中不包括联盟信息，说明该链结构是应用链，同样调用 newChainSupport\(\) 方法生成链支持结构，并通过链的 start\(\) 方法启动。

### NewServer

新建一个 Server 结构，包括一个 broadcast 的处理句柄，以及一个 deliver 的处理句柄。

```go
type server struct {
	bh    broadcast.Handler
	dh    deliver.Handler
	debug *localconfig.Debug
	*multichannel.Registrar
}
```

NewServer 分别初始化这两个句柄，挂载上前面初始化的 multichannel.Registrar 结构。broadcast 句柄还需要初始化一个配置更新的处理器，负责处理 CONFIG\_UPDATE 交易。

```go
func NewServer(r *multichannel.Registrar, _ crypto.LocalSigner, debug *localconfig.Debug, timeWindow time.Duration, mutualTLS bool) ab.AtomicBroadcastServer {
	s := &server{
		dh:        deliver.NewHandlerImpl(deliverSupport{Registrar: r}, timeWindow, mutualTLS),
		bh:        broadcast.NewHandlerImpl(broadcastSupport{Registrar: r}),
		debug:     debug,
		Registrar: r,
	}
	return s
}
```



