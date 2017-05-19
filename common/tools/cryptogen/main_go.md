#### main.go
核心代码十分简单。

```go
//command line flags
var (
	app = kingpin.New("cryptogen", "Utility for generating Hyperledger Fabric key material")

	gen        = app.Command("generate", "Generate key material")
	outputDir  = gen.Flag("output", "The output directory in which to place artifacts").Default("crypto-config").String()
	configFile = gen.Flag("config", "The configuration template to use").File()

	showtemplate = app.Command("showtemplate", "Show the default configuration template")
)

func main() {
	kingpin.Version("0.0.1")
	switch kingpin.MustParse(app.Parse(os.Args[1:])) {

	// "generate" command
	case gen.FullCommand():
		generate()

	// "showtemplate" command
	case showtemplate.FullCommand():
		fmt.Print(defaultConfig)
		os.Exit(0)
	}

}
```


##### generate

主要调用 generate() 方法。

* 首先调用 getConfig()读取本地配置或使用默认配置。
* 使用 renderOrgSpec() 和 generatePeerOrg()方法依次生成每个 peer 类型的组织。
* 使用 renderOrgSpec() 和 generateOrdererOrg()方法依次生成每个 orderer 类型的组织。

renderOrgSpec()负责将模板中具体组织数据解析出来。

##### peer 组织
generatePeerOrg（）会具体生成每个 peer 类型的组织，结构如下。

peerOrganizations：
* org1
  * ca：存放组织根证书和对应的私钥文件，默认采用 EC 算法，证书为自签名。组织内的实体将基于该根证书作为相同的证书根。
  * msp：存放代表该组织的身份信息
    * admincerts：组织管理员的证书。
    * cacerts：组织的根证书。
    * keystore：组织的私钥文件，用来签名。
    * signcerts：组织的签名验证证书。
  * peers：存放该组织下的所有 peer 节点
    * peer1：第一个 peer 的信息，包括 msp 证书和 tls 证书两类。
      * msp：
        * admincerts：组织管理员的证书。
        * cacerts：存放组织的根证书。
        * keystore：空
        * signcerts：存放组织的根证书。
      * tls：
  * users：存放属于该组织的用户
* org2
  * ...

注意每个实体（组织、节点、用户）最终都会拥有 MSP 来代表身份信息，其中包括三种证书：管理员身份的证书、信任 CA 的根证书、代表自己的证书（检查签名）。此外，还可以包括代表自己身份的签名用的私钥。

GenerateVerifyingMSP()会生成

