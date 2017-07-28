#### lccc.go

LSCC---Lifecycle System Chaincode, 负责chaincode的全生命周期管理。

Invoke操作有INSTALL、DEPLOY(语义上的实例化)、UPGRADE、GETCCINFO、GETDEPSPEC、GETCCDATA、GETCHAINCODES、GETINSTANTIATEDCHAINCODES。

| 操作 | 参数 |
| -- | -- |
| INSTALL | 1.ChaincodeDeploymentSpec |
| DEPLOY <br> UPGRADE | 1.chainName <br> 2.ChaincodeDeploymentSpec <br> 3.SignaturePolicyEnvelope(endorsement policy) <br> 4.escc <br> 5.vscc|
| GETCCINFO <br> GETDEPSPEC <br> GETCCDATA | 1.chainName <br> 2.ccName |
| GETCHAINCODES <br> GETINSTANTIATEDCHAINCODES | None |

##### INSTALL

- 验证SignedProposal是不是admain节点签署的（check时传入的ADMINS）。
- executeInstall()
    - 检查cc名字和版本是否合法，不包含非法字符。
    - 把ccPackage写入文件系统。

#### DEPLOY

- 检查参数，如果空则赋值默认值。
- executeDeploy()
