## main.go

peer 服务是主服务。

该服务支持各种 peer 命令。

包括查询状态，和启动、停止节点服务等。

### serve 函数

最重要的是 `func serve(args []string) error` 函数。

当执行 `peer node start` 命令时候被调用，启动一个节点服务。

首先是进行配置管理，根据配置信息和一些计算来构建 cache 结构，探测节点信息等。

主要调用 core.peer 包来实现。

```go
	if err := peer.CacheConfiguration(); err != nil {
		return err
	}

	peerEndpoint, err := peer.GetPeerEndpoint()
	if err != nil {
		err = fmt.Errorf("Failed to get Peer Endpoint: %s", err)
		return err
	}
```

之后是启动 grpc 服务，监听到 7051 端口。

```go
	listenAddr := viper.GetString("peer.listenAddress")

	if "" == listenAddr {
		logger.Debug("Listen address not specified, using peer endpoint address")
		listenAddr = peerEndpoint.Address
	}

	lis, err := net.Listen("tcp", listenAddr)
```

创建 EventHub 服务，通过调用 createEventHubServer() 方法来实现，该服务也是 grpc，只有 vp 节点才开启。

```go
	lis, err = net.Listen("tcp", viper.GetString("peer.validator.events.address"))
	if err != nil {
		return nil, nil, fmt.Errorf("failed to listen: %v", err)
	}

	//TODO - do we need different SSL material for events ?
	var opts []grpc.ServerOption
	if comm.TLSEnabled() {
		creds, err := credentials.NewServerTLSFromFile(viper.GetString("peer.tls.cert.file"), viper.GetString("peer.tls.key.file"))
		if err != nil {
			return nil, nil, fmt.Errorf("Failed to generate credentials %v", err)
		}
		opts = []grpc.ServerOption{grpc.Creds(creds)}
	}

	grpcServer = grpc.NewServer(opts...)
	ehServer := producer.NewEventsServer(uint(viper.GetInt("peer.validator.events.buffersize")), viper.GetInt("peer.validator.events.timeout"))
	pb.RegisterEventsServer(grpcServer, ehServer)
```

启动数据库服务。

```go
	db.Start()
```

启动一个 grpc 服务。

```go
grpcServer := grpc.NewServer(opts...)
```


注册 Chaincode 支持服务。

```go
	

	secHelper, err := getSecHelper()
	if err != nil {
		return err
	}

	secHelperFunc := func() crypto.Peer {
		return secHelper
	}

	registerChaincodeSupport(chaincode.DefaultChain, grpcServer, secHelper)
```

创建 peer 节点服务，注意区分 vp 和 nvp 节点。

```go
	if peer.ValidatorEnabled() {
		logger.Debug("Running as validating peer - making genesis block if needed")
		makeGenesisError := genesis.MakeGenesis()
		if makeGenesisError != nil {
			return makeGenesisError
		}
		logger.Debugf("Running as validating peer - installing consensus %s", viper.GetString("peer.validator.consensus"))
		peerServer, err = peer.NewPeerWithEngine(secHelperFunc, helper.GetEngine)
	} else {
		logger.Debug("Running as non-validating peer")
		peerServer, err = peer.NewPeerWithHandler(secHelperFunc, peer.NewPeerHandler)
	}
```

分别注册 peer 节点服务、管理服务、devops 服务、openchain 服务等，如果开启 REST，则启动 REST 服务。

```go
	pb.RegisterPeerServer(grpcServer, peerServer)

	// Register the Admin server
	pb.RegisterAdminServer(grpcServer, core.NewAdminServer())

	// Register Devops server
	serverDevops := core.NewDevopsServer(peerServer)
	pb.RegisterDevopsServer(grpcServer, serverDevops)

	// Register the ServerOpenchain server
	serverOpenchain, err := rest.NewOpenchainServerWithPeerInfo(peerServer)
	if err != nil {
		err = fmt.Errorf("Error creating OpenchainServer: %s", err)
		return err
	}

	pb.RegisterOpenchainServer(grpcServer, serverOpenchain)

	// Create and register the REST service if configured
	if viper.GetBool("rest.enabled") {
		go rest.StartOpenchainRESTServer(serverOpenchain, serverDevops)
	}
```

最后，启动这些服务的 grpc 服务端，启动 EventHub 的服务端。如果需要 profiling，还会打开监听服务。
