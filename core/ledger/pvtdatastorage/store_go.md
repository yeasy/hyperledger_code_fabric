#### store.go

该包中的 `Store` 接口，用于管理账本的私有写集数据的持久化。
因为pvt数据和账本的blocks须要同步写入，两者的数据在逻辑上应该是原子的。这个 `Store`的实现
需要提供两阶段（比如commit/rollback）提交的能力。首先private data数据会通过`Prepare`函数
发送给`Store`，等block写入到存储中，根据block写入成功与否，再调用`Commit`或者`Rollback`函数。
这个Store的实现需要保证在`Prepare`和`Commit/Rollback`之间程序故障也能正常工作。

