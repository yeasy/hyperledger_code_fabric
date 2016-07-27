## block.go

Block 结构是在 fabric.proto 中定义，这里实现了它支持的一些方法。

主要包括：

* Bytes()：以数组形式返回 block 内容；
* GetHash()：返回一个 block 的 hash 值；
* GetStateHash()：返回 block 中的 StateHash 变量值；
* SetPreviousBlockHash()：设置 block 的 PreviousBlockHash 变量值；

