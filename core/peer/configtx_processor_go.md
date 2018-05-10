### configtx\_processor.go

交易处理器实现，实现github.com/hyperledger/fabric/core/ledger/customtx/Processor接口。

#### GenerateSimulationResults

github.com/hyperledger/fabric/core/ledger/customtx/Processor接口里的函数。首先从envelope取出交易类型，然后根据交易类型分别处理，其只处理2种交易类型：

* HeaderType\_CONFIG：调用processChannelConfigTx处理，简单地将配置存储在StateDb中。另外，如果交易来自创世块，则存储资源配置种子。

* PEER\_RESOURCE\_UPDATE：调用processResourceConfigTxDuringInitialization处理，在正常过程中，这将验证当前资源绑定的交易，计算完整配置，并在发现交易有效时存储完整配置。如果initializingLedger参数为true，使用来自StateDB的最新配置计算完整配置。

#### processChannelConfigTx

* 保存channel配置信息到statedb中
* 判断是第一个创世块的交易配置，则存储资源配置种子。





