#### statedb

VersionedDB 接口是对版本化KVS的接口抽象。提供如：

- 查询版本化状态
- 获取某个key的版本信息
- 范围查询
- 根据query参数，进行自定义查询
- 批量更新
- 获取最新的版本号

它的实现有leveldb和couchdb。

###### stateleveldb

stateleveldb的实现主要依赖于leveldbhelper.DBHandle属性的能力，做了一层版本化逻辑的封装。
stateleveldb不支持自定义query的查询。

###### statelcouchdb

statelcouchdb的实现的driver是couchdb.CouchInstance。于stateleveldb相比几个不同之处：

1. 支持自定义的query查询。
2. 本地支持对提交的状态变更进行缓存。
3. key只支持utf-8的字串
4. 为部署链码建立索引。
