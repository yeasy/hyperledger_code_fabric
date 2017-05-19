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
* org1：存放第一个组织的相关材料，每个组织会生成单独的根证书。
  * ca：存放组织的根证书和对应的私钥文件，默认采用 EC 算法，证书为自签名。组织内的实体将基于该根证书作为相同的证书根。
  * msp：存放代表该组织的身份信息
    * admincerts：组织管理员的身份验证证书。
    * cacerts：组织的根证书。
    * keystore：组织的私钥文件，用来签名。
    * signcerts：组织的签名验证证书。
  * peers：存放该组织下的所有 peer 节点
    * peer1：第一个 peer 的信息，包括 msp 证书和 tls 证书两类。
      * msp：
        * admincerts：组织管理员的身份验证证书。
        * cacerts：存放组织的根证书。
        * keystore：本节点的身份私钥，用来签名。
        * signcerts：验证本节点签名的证书，被组织根证书签名。
      * tls：存放 tls 相关的证书和私钥
        * ca.crt：组织的根证书。
        * server.crt：验证本节点签名的证书，被组织根证书签名。
        * server.key：本节点的身份私钥，用来签名。
  * users：存放属于该组织的用户的实体。
    * Admin：管理员用户的信息，包括 msp 证书和 tls 证书两类。
      * msp：
        * admincerts：组织根证书作为管理者身份验证证书。
        * cacerts：存放组织的根证书。
        * keystore：本用户的身份私钥，用来签名。
        * signcerts：管理员用户的身份验证证书，被组织根证书签名。
      * tls：存放 tls 相关的证书和私钥
        * ca.crt：组织的根证书。
        * server.crt：管理员的用户身份验证证书，被组织根证书签名。
        * server.key：管理员用户的身份私钥，用来签名。
    * User1：第一个用户的信息，包括 msp 证书和 tls 证书两类。
      * msp：
        * admincerts：组织根证书作为管理者身份验证证书。
        * cacerts：存放组织的根证书。
        * keystore：本用户的身份私钥，用来签名。
        * signcerts：验证本用户签名的身份证书，被组织根证书签名。
      * tls：存放 tls 相关的证书和私钥
        * ca.crt：组织的根证书。
        * server.crt：验证用户签名的身份证书，被组织根证书签名。
        * server.key：用户的身份私钥，用来签名。
* org2
  * ...

注意每个实体（组织、节点、用户）最终都会拥有 MSP 来代表身份信息，其中包括三种证书：管理员身份的验证证书、实体信任的 CA 的根证书、自身身份验证（检查签名）的证书。此外，还包括对应身份验证的签名用的私钥。

GenerateVerifyingMSP()会生成组织的 MSP 信息。

generateNodes 会针对每个实体生成所需要的 msp 和 tls 信息，证书都会被组织根证书签名，信任的根都是组织 CA 证书。

###### orderer 组织
跟 peer 组织类似过程。

