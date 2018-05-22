#### blockwriter.go

创建区块文件，写入本地账本。

```
type BlockWriter struct {
    support            blockWriterSupport
    registrar          *Registrar
    lastConfigBlockNum uint64
    lastConfigSeq      uint64
    lastBlock          *cb.Block
    committingBlock    sync.Mutex
}
```

BlockWriter会使用一个锁和在写入文件时创建出一个committer协程写入来保证不会出现并发问题。

#### CreateNextBlock

使用传入的内容和下一个区块号创建一个新的区块。

#### WriteConfigBlock

解析区块中的envelope配置信息，得到channel header类型

* HeaderType\_ORDERER\_TRANSACTION类型，新建channel控制信息和管理handler，

* HeaderType\_CONFIG类型，更新channel的配置信息

* 最后调用WriteBlock写入区块链。

#### WriteBlock

获得bw.committingBlock互斥锁，起一个go协程进行区块写入，完成后释放互斥锁。

#### commitBlock

只有获得bw.committingBlock互斥锁的线程才能调用此函数。将最新的区块进行签名，然后加到区块链上。

