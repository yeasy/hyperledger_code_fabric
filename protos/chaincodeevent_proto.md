## chaincodeevent.proto

跟 Chaincode 相关的事件。

```
message ChaincodeEvent {
      string chaincodeID = 1;
      string txID = 2;
      string eventName = 3;
      bytes payload = 4;
}
```