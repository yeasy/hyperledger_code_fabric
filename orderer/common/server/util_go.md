#### util.go

几个通用应用函数。

#### createLedgerFactory

根据配置的账本类型，创建账本工厂实例。

* 配置类型为文件账本，调用fileledger.New\(ld\)创建
* 配置类型为json账本，调用jsonledger.New\(ld\)
* 其它场景创建为内存账本，调用ramledger.New\(int\(conf.RAMLedger.HistorySize\)\)



#### createTempDir

#### createSubDir

调用os函数创建临时或子目录。

