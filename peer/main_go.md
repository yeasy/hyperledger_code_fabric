## main.go

主服务，所有 peer 命令都从这里入口。

各个子命令的处理在对应子包中，如

* node：node 对应子命令
* chaincode：chaincode 对应子命令
* channel：channel 对应子命令


main 函数主要完成命令的注册和一些初始化配置工作。

首先，从本地的 yaml、环境变量以及命令行选项中读取配置信息；之后注册各个子命令；最后初始化 MSP 部分。

peer 的 MSP 文件路径在 `peer.mspConfigPath`；默认的 mspID 是 `peer.localMspId`。

### MSP 初始化

通过 peer/common/common.go 文件中的 InitCrypto 方法完成。

```golang
func InitCrypto(mspMgrConfigDir string, localMSPID string) error {
	// Init the BCCSP
	var bccspConfig *factory.FactoryOpts
	err := viperutil.EnhancedExactUnmarshalKey("peer.BCCSP", &bccspConfig)
	if err != nil {
		return fmt.Errorf("Could not parse YAML config [%s]", err)
	}

	err = mspmgmt.LoadLocalMsp(mspMgrConfigDir, bccspConfig, localMSPID)
	if err != nil {
		return fmt.Errorf("Fatal error when setting up MSP from directory %s: err %s\n", mspMgrConfigDir, err)
	}

	return nil
}
```



