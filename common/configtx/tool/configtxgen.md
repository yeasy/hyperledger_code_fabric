#### configtxgen
configtxgen 工具是一个很重要的离线辅助工具，它的主要功能有如下几个：

* 生成启动 orderer 需要的初始区块，并检查区块内容；
* 生成创建 channel 需要的配置交易，并检查交易内容；
* 生成锚点 Peer 的更新配置信息。

默认情况下，configtxgen 工具会依次尝试从 `$FABRIC_CFG_PATH` 环境变量指定的路径，当前路径和 `/etc/hyperledger/fabric` 路径下查找 configtx.yaml 配置文件并读入。

main.go 文件为入口。

核心代码十分简单：

* 首先通过 `factory.InitFactories(nil)` 读入 BCCSP 配置；
* 通过 `config := genesisconfig.Load(profile)` 读入 configtx.yaml 配置文件；
* 然后根据指定的参数分别进行相应的操作。


```go
func main() {
	var outputBlock, outputChannelCreateTx, profile, channelID, inspectBlock, inspectChannelCreateTx, outputAnchorPeersUpdate, asOrg string

	flag.StringVar(&outputBlock, "outputBlock", "", "The path to write the genesis block to (if set)")
	flag.StringVar(&channelID, "channelID", provisional.TestChainID, "The channel ID to use in the configtx")
	flag.StringVar(&outputChannelCreateTx, "outputCreateChannelTx", "", "The path to write a channel creation configtx to (if set)")
	flag.StringVar(&profile, "profile", genesisconfig.SampleInsecureProfile, "The profile from configtx.yaml to use for generation.")
	flag.StringVar(&inspectBlock, "inspectBlock", "", "Prints the configuration contained in the block at the specified path")
	flag.StringVar(&inspectChannelCreateTx, "inspectChannelCreateTx", "", "Prints the configuration contained in the transaction at the specified path")
	flag.StringVar(&outputAnchorPeersUpdate, "outputAnchorPeersUpdate", "", "Creates an config update to update an anchor peer (works only with the default channel creation, and only for the first update)")
	flag.StringVar(&asOrg, "asOrg", "", "Performs the config generation as a particular organization, only including values in the write set that org (likely) has privilege to set")

	flag.Parse()

	logging.SetLevel(logging.INFO, "")

	logger.Info("Loading configuration")
	factory.InitFactories(nil)
	config := genesisconfig.Load(profile)

	if outputBlock != "" {
		if err := doOutputBlock(config, channelID, outputBlock); err != nil {
			logger.Fatalf("Error on outputBlock: %s", err)
		}
	}

	if outputChannelCreateTx != "" {
		if err := doOutputChannelCreateTx(config, channelID, outputChannelCreateTx); err != nil {
			logger.Fatalf("Error on outputChannelCreateTx: %s", err)
		}
	}

	if inspectBlock != "" {
		if err := doInspectBlock(inspectBlock); err != nil {
			logger.Fatalf("Error on inspectBlock: %s", err)
		}
	}

	if inspectChannelCreateTx != "" {
		if err := doInspectChannelCreateTx(inspectChannelCreateTx); err != nil {
			logger.Fatalf("Error on inspectChannelCreateTx: %s", err)
		}
	}

	if outputAnchorPeersUpdate != "" {
		if err := doOutputAnchorPeersUpdate(config, channelID, outputAnchorPeersUpdate, asOrg); err != nil {
			logger.Fatalf("Error on inspectChannelCreateTx: %s", err)
		}
	}
}
```

##### doOutputBlock

生成初始区块，存放到指定路径。

* 首先调用 provisional.New(config) 根据读入的配置生成启动参数结构。
* 调用 	genesisBlock := pgen.GenesisBlockForChannel(channelID) 生成指定通道的区块，核心数据是一个 ConfigEnvelope 结构。
* 将区块文件写到本地。

##### doOutputChannelCreateTx

生成创建新通道的交易，存放到指定路径。

* 获取签名实体信息。
* 生成创建新通道的交易结构。
* 写入到本地文件。

##### doInspectBlock

读入区块内容，并解析为 ConfigEnvelope 结构，打印出来。

##### doInspectChannelCreateTx

读入交易文件内容，并解析为 ConfigUpdateEnvelope 结构，打印出来。

##### doOutputAnchorPeersUpdate

生成一个锚点 Peer 更新的 ConfigUpdateEnvelope，并存储到指定路径。

