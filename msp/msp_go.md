## msp.go

定义了一些基础功能接口，包括 

* MSPManager：管理 MSP
* MSP：服务提供者的抽象，提供签名、校验等功能。
* Identity：跟证书相关的操作，包括获取内容，校验内容等。
* SigningIdentity：继承自 Identity，进一步支持签名功能。
