#### fsblkstorage 包

fsblkstorage包下主要实现了两个对外接口：BlockStoreProvider和BlockStore，
一个内部对象：blockfileMgr。

##### fsBlockStore实现BlockStore接口
fsBlockStore是BlockStore接口的实现，具有添加区块，查询区块链信息，各种关键字查询区块、各种关键字
查询交易的功能。实际上它是对blockfileMgr的简单封装，实现逻辑均在blockfileMgr中。

##### FsBlockstoreProvider实现BlockStoreProvider接口
BlockStore是和账本一一对应，BlockStoreProvider提供对BlockStore的管理（是非线程安全的，在使用上一个ledger
只能调用一次OpenBlockStore，这里还能做代码上的优化）

##### blockfileMgr内部对象

blockfileMgr对象实现了文件系统中区块加载、读写、索引查询功能，是这个包里的核心对象。
这个对象方法除了组装逻辑，还依赖了其他几个重要的内部接口和数据：index，blockfileStream和checkpointInfo。

```go
type blockfileMgr struct {
	rootDir           string  //文件根目录（chains/{ledger_id}）
	conf              *Conf   //配置包括其他ledger的文件根目录，blockfile文件最大大小
	db                *leveldbhelper.DBHandle //用于在本地leveldb中存储检查点，也用来存储索引。
	index             index //索引，实际上是一个字典，从配置而来，表示这个ledger需要支持哪些属性的索引
	cpInfo            *checkpointInfo //检查点
	cpInfoCond        *sync.Cond  
	currentFileWriter *blockfileWriter //写输出流，从已经存在的文件之后写。
	bcInfo            atomic.Value  //一个原子对象，存储了当前账本的高度（最新的区块编号+1），最新区块的头部hash，倒数第二个区块的hash
}
```

初始化过程：

1. 加载当前的检查点，并保存到db中。
2. 根据检查点生成写输出流，保存bcInfo。
3. 如果链非空，同步db中的索引。

核心功能：

增加区块

1. 检查区块的编号和当前的高度是否一致。
2. 计算区块写入字节的大小 + 描述区块大小的字节数 + 文件指针偏移量是否大于规定的总大小。
3. 如果是，blockfileMgr.moveToNextFile()，会更新currentFileWriter，cpInfo。
4. 写入区块。
5. 更新和保存检查点。
6. 在db中索引区块信息。
7. 更新bcInfo。

查询交易

1. 根据index对象查询到交易的location。
2. 打开对应文件流，跳过offset，读取bytesLength长度。
3. 封装成envelope结构返回。

###### 索引
blockIndex提供了索引和查询的相关功能。

```go
type blockIndex struct {
	indexItemsMap map[blkstorage.IndexableAttr]bool //需要索引的属性列表
	db            *leveldbhelper.DBHandle            
}
```

核心功能： 索引区块

1. 入参是blockIdxInfo，包含信息区块编号，hash值，区块在文件中的位置信息，交易在区块中的位置列表。
2. 有6个索引，先检查索引在不在indexItemsMap字典，如果在，则需要索引。
3. 根据索引key的不同含义(不同的索引在数据库中，key的第一个字符不同)，向db中写入record。而value一般是
   {blockfile的编号，在文件中的偏移位置，内容的长度}，用以在文件系统中定位到某个对象（一般是是区块或者交易）。
4. 支持的索引：区块hash值-区块位置，区块号-区块位置，交易号-交易位置，（区块号，交易号）—交易位置，交易号-区块位置
   交易号-交易校验码



###### 从区块文件构建检查点信息

检查点信息包含：

```
{
	latestFileChunkSuffixNum int //文件的最大编号
	latestFileChunksize      int //文件中最后一个block的偏移位置
	isChainEmpty             bool //整个系统是否无block
	lastBlockNumber          uint64 //最后一个区块的编号
}
```

1. 如果db中没有blkMgrInfo的信息
    - 返回文件系统中blockfile_文件的最大编号
    - 从最大编号blockfile_文件中读取最后一个block
    - lastBlockNumber赋值为最后这个block头部中的编号
2. 如果db中有blkMgrInfo的信息
    - 根据提供的最后一个文件编号，读取文件，刷新检查点其他字段
    
检查点的信息会存在名称为这个ledgerId的db中，key是"blkMgrInfo"

###### blockfileStream

blockfileStream是对单一blockfile文件，提供根据协议读出每个block字节数组，和block在文件中位置信息的对象。
block在blockfile中存储的协议是：

`[ {8个字节(表示block0的大小), block0内容字节数组},..., {8个字节(表示blocki的大小), blocki内容字节数组}]`

```go
type blockfileStream struct {         
	fileNum       int       //blockfile的编号      
	file          *os.File  //文件对象      
	reader        *bufio.Reader   //读文件流
	currentOffset int64           //当前读指针偏移位置
}                                     
```

blockStream对象是对blockfileStream的扩展，支持从多个blockfile文件中依次读取出block内容。

