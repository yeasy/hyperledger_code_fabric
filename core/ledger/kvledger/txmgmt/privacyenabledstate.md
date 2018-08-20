#### privacyenabledstate

该包下DB接口是VersionedDB接口的扩展，额外提供的功能有：

1. 根据key名称查询private data
2. 通过key的hash值查询private data
3. 根据key的hash值，查询value的版本信息
4. 多key查询和范围查询。
5. 在私有数据上执行自定义query查询
6. 私有数据更新。
7. 从缓存中根据hash查询value版本。