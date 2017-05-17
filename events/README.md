# events
EventHub 服务处理相关的模块。

Event 包括四种类型：

```protobuf
enum EventType {
        REGISTER = 0;
        BLOCK = 1;
	CHAINCODE = 2;
	REJECTION = 3;
}
```