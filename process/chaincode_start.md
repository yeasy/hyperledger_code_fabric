## Chaincode 启动过程

### Chaincode 简介

这里讲的 Chaincode 是用户链码（User Chaincode，UCC），对应用开发者来说十分重要，它提供了基于区块链分布式账本的状态处理逻辑，基于它可以开发出多种复杂的应用。

Hyperledger Fabric 中，Chaincode 默认运行在 Docker 容器中。Peer 通过调用 Docker API 来创建和启动 Chaincode 容器。

### 典型结构

下面给出了链码的典型结构，用户只需要关注到 Init() 和 Invoke() 函数的实现上，在其中利用 shim.ChaincodeStubInterface 结构，实现跟账本的交互逻辑。

```go
package main

import (
	"errors"
	"fmt"
	"github.com/hyperledger/fabric/core/chaincode/shim"
)

type DemoChaincode struct { }

func (t *DemoChaincode) Init(stub shim.ChaincodeStubInterface) pb.Response {
	// more logics using stub here
	return stub.Success(nil)
}

func (t *DemoChaincode) Invoke(stub shim.ChaincodeStubInterface) pb.Response
	// more logics using stub here
	return stub.Success(nil)
}

func main() {
	err := shim.Start(new(DemoChaincode))
	if err != nil {
		fmt.Printf("Error starting DemoChaincode: %s", err)
	}
}
```

### 启动过程
