## 链码安装过程

链码安装主要包括两个部分：

* 客户端封装安装消息；
* Peer 节点处理请求。

### 客户端封装安装消息

客户端将链码的源码和环境等内容封装为一个链码安装打包文件（Chaincode Install Package，CIP），并传输到指定的 Peer 节点。此过程只需要跟 Peer 节点打交道。

主要步骤包括：

* 首先是构造带签名的提案结构（SignedProposal）。
	* 调用 `InitCmdFactory(isEndorserRequired, isOrdererRequired bool) (*ChaincodeCmdFactory, error)` 方法，初始化 EndoserClient（跟 Peer 通信）、BroadcastClient（跟 Orderer 通信）、Signer（签名操作）等辅助结构体。所有链码子命令都会执行该过程，会根据需求具体初始化不同的结构。
	* 然后根据命令行参数进行解析，判断是根据传入的打包文件来直接读取 ChaincodeDeploymentSpec（CDS）结构，还是根据传入参数从本地链码源代码文件来构造生成。
	* 以本地重新构造情况为例，首先根据命令行中传入的路径、名称等信息，构造生成 ChaincodeSpec（CS）结构。
	* 利用 ChaincodeSpec 结构，结合链码包数据生成一个 ChaincodeDeploymentSpec 结构（chainID 为空），调用本地的 `install(msg proto.Message, cf *ChaincodeCmdFactory) error` 方法。
	* install 方法基于传入的 ChaincodeDeploymentSpec 结构，构造一个对生命周期管理系统链码（LSCC）调用的 ChaincodeSpec 结构，其中，Type 为 ChaincodeSpec_GOLANG，ChaincodeId.Name 为“lscc”，Input 为 “install”+ChaincodeDeploymentSpec。进一步地，构造了一个 LSCC 的 ChaincodeInvocationSpec（CIS）结构，对 ChaincodeSpec 结构进行封装。
	* 基于 LSCC 的 ChaincodeInvocationSpec 结构，添加头部结构，生成一个提案（Proposal）结构。其中，通道头部中类型为 ENDORSER_TRANSACTION，TxID 为对随机数+签名实体，进行 Hash。
	* 对 Proposal 进行签名，转化为一个签名后的提案消息结构 SignedProposal。
* 将带签名的提案结构通过 EndorserClient 经由 gRPC 通道发送给 Peer 的 `ProcessProposal(ctx context.Context, in *SignedProposal, opts ...grpc.CallOption) (*ProposalResponse, error)` 接口。
* Peer 模拟运行生命周期链码的调用交易进行处理，检查格式、签名和权限等，通过则保存到本地文件系统。

下图给出了链码安装过程中最为重要的 SignedProposal 数据结构，该结构对于大部分链码操作命令都是类似的，其中最重要的是 ChannelHeader 结构和 ChaincodeSpec 结构中参数的差异。

![链码安装过程中所涉及的数据结构](_images/chaincode_install_structure.png)