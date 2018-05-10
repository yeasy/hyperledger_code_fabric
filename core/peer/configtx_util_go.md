### configtx\_util.go

配置交易的一些通用函数。

#### validateAndApplyResourceConfig

验证RealCyCopFigTX与当前资源配置没有任何读写冲突，并通过将交易里的变化差异应用到当前配置来准备完整的配置。

最后，设置完整的配置。

#### extractFullConfigFromSeedTx

从创世块中的配置交易中取出种子资源配置。

#### computeFullConfig

计算当前资源和交易（包含差异）的全部资源配置。

#### retrievePersistedResourceConfig

从StateDB检索持久化资源配置。

#### retrievePersistedChannelConfig

从StateDB检索持久化channel配置。

