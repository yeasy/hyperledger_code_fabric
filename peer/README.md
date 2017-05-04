# peer
主命令模块。

作为服务端时候，支持 node 子命令；作为命令行时候，支持 chaincode、channel 等子命令。

作为命令行时候，会维持一个 ChaincodeCmdFactory 结构。

```golang
type ChaincodeCmdFactory struct {
	EndorserClient  pb.EndorserClient
	Signer          msp.SigningIdentity
	BroadcastClient common.BroadcastClient
}
```

其中：

* EndorserClient 是跟 `peer.address` 指定地址通信的 grpc 通道；
* Signer 为 LocalMSP 中的默认签名实体；
* BroadcastClient 是连接到通过 `-o` 指定的 orderer 服务的 grpc 通道。