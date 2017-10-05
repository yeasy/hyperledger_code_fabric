## Orderer 节点启动

Orderer 节点启动通过 `orderer` 包下的 main() 方法实现，会进一步调用到 `orderer/common/server 包中的 Main() 方法`。

核心代码如下所示。

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

包括配置初始化过程和核心启动过程两个部分：
* config.Load()：从本地配置文件和环境变量中读取配置信息，构建配置树结构。
* initializeLoggingLevel(conf)：配置日志级别。
* initializeLocalMsp(conf)：配置 MSP 结构。
* Start()：完成启动后的核心工作。

### 整体过程

核心启动过程都在 `orderer/common/server`包中的 Start() 方法，如下图所示。

![orderer.common.server 包中的 Main() 方法](_images/orderer_common_server_Start.png)

Start() 方法会初始化 gRPC 服务需要的结构，然后启动服务。

核心代码如下所示。

```go
func Start(cmd string, conf *config.TopLevel) {
	logger.Debugf("Start()")
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

### gRPC 服务结构初始化

包括创建新的 MSP 签名结构，初始化 Registrar 结构来管理各个账本结构，以及创建 gRPC 服务端结构。

```go
signer := localmsp.NewSigner() // 初始化签名结构
manager := initializeMultichannelRegistrar(conf, signer) // 初始化账本管理器结构
server := NewServer(manager, signer, &conf.Debug) // 创建 gRPC 服务端
```

*说明：Registrar 结构（位于 `orderer.common.multichannel` 包）是 Orderer 组件中最核心的结构，管理了 Orderer 中所有的账本、共识插件等数据结构。*

`initializeMultichannelRegistrar(conf, signer)` 方法十分关键，核心代码如下：

```go
func initializeMultichannelRegistrar(conf *config.TopLevel, signer crypto.LocalSigner) *multichannel.Registrar {
	// 创建账本操作的工厂结构
	lf, _ := createLedgerFactory(conf)
	
	// 如果是新启动情况，创建系统通道的账本结构
	if len(lf.ChainIDs()) == 0 {
		logger.Debugf("There is no chain, hence we must be in bootstrapping")
		initializeBootstrapChannel(conf, lf)
	} else {
		logger.Info("Not bootstrapping because of existing chains")
	}
	//初始化共识插件
	consenters := make(map[string]consensus.Consenter)
	consenters["solo"] = solo.New()
	consenters["kafka"] = kafka.New(conf.Kafka.TLS, conf.Kafka.Retry, conf.Kafka.Version, conf.Kafka.Verbose)

	// 启动各个链结构上的维护者（chainSupport）结构
	return multichannel.NewRegistrar(lf, consenters, signer)
}
```

利用传入的配置信息和签名信息完成如下步骤：

* 创建账本操作的工厂结构；
* （可选）如果是新启动情况，利用给定的系统初始区块文件初始化系统通道相关结构；
* 完成共识插件（包括 `solo` 和 `kafka` 两种）的初始化；
* `NewRegistrar(lf, consenters, signer)`
方法会扫描本地账本数据（至少已存在系统通道），创建 Registrar 结构，为每个账本都启动维护者结构。


#### 创建 Registrar 结构

`NewRegistrar(lf, consenters, signer)` 方法位于 `orderer.common.multichannel` 包，核心代码如下：

```go
existingChains := ledgerFactory.ChainIDs()
for _, chainID := range existingChains {
	if _, ok := ledgerResources.ConsortiumsConfig(); ok { // 如果是系统账本
	chain := newChainSupport(r, ledgerResources, consenters, signer)
	chain.Processor = msgprocessor.NewSystemChannel(chain, r.templator, msgprocessor.CreateSystemChannelFilters(r, chain))
	r.chains[chainID] = chain
	r.systemChannelID = chainID
	r.systemChannel = chain
	defer chain.start()
	else // 如果是应用账本
	chain := newChainSupport(r, ledgerResources, consenters, signer)
	r.chains[chainID] = chain
	chain.start()
	}
```

### gRPC 服务启动


