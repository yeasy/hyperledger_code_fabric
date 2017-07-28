#### validator.go

主要实现 txValidator 结构体。

```go
type txValidator struct {
	support Support
	vscc    vsccValidator
}
```

该结构体提供了 `func (v *txValidator) Validate(block *common.Block) error` 方法，对给定区块进行合法性检查。

主要过程如下：
* 创建一个交易过滤器，初始时给区块中所有交易都打上valid标签，然后依次检查每笔交易，再修改标签，该过滤器为了方便之后commit过程筛选有效交易。
* 对区块中的每笔交易(env)都执行`ValidateTransaction()`, 其主要内容是检查交易的组成的完整性，签名的正确性，交易ID的正确性（检查重复交易的前提）以及交易请求Proposal的一致性（比较hash）。
* 检查交易所在通道是否存在。
* 若通道头的类型是背书交易，通过TxID检验交易是否已经存在，若否然后执行`VSCCValidateTx()`，最后通过获取交易instance来标记下是invoke交易还是upgrade交易。`VSCCValidateTx()`主要内容是获取交易读写集，检查写集的合法性（非SCC的话不能对LSCC和不可调用的SCC写入，SCC的话如果自身不能从外部调用也不可以写入），获取对应的VSCC和policy，发起对VSCC的调用来验证。
* 若通道头的类型是配置，执行`Apply()`去应用配置。
* 根据标记的invoke交易和upgrade交易，若有同一cc的多个upgrade交易，保留最后一个，前面的都标记为非法; 若invoke交易对应的cc（在该区块中）存在upgrade交易，则把所有该cc的invoke交易标记为非法。
* 把交易过滤器添加进block的metadata中，用于之后的commit过程。
