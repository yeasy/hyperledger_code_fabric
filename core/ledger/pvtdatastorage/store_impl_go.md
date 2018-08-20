#### store_impl.go

`store` 是对`Store`的实现。

#### Prepare

1. 入参pvData被拆分为dataEntries和exporyEntries
2. 前者是所有的私有数据，后者是带有BTL(block time to live)的私有数据。
3. dataEntries、exporyEntries和pendingCommitKey(表示正在等待commit)原子操作写入db。
4. 设置pending状态位为true。


#### commit

1. 删除db中pendingCommitKey状态位，更新db的lastCommittedBlkkey为新的区块号码。
2. 更新对象中的batchPendind为false，lastCommittedBlock为最新的区块号。
3. 触发协程，遍历所有的设置BTL的数据，发现过期数据进行删除，同时删除普通数据集中对应的条目。

#### Rollback

1. 简单将batchPending设置为false。这个区块的写操作会重试，因此没有必要删除已经写入db的数据，
   后面来的区块必须等这个写入失败的区块成功之后才能写入。
   
