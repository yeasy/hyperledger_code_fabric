## events.proto
定义事件相关的消息和接口。

对于事件来说，包括两种角色，生产者（Producer）和消费者（Consumer）。

生产者会广播事件，消费者可以把自己注册到感兴趣的主题上。

核心的代码为

```
message Event {
    //TODO need timestamp

    oneof Event {
        //consumer events
        Register register = 1;

        //producer events
        Block block = 2;
        ChaincodeEvent chaincodeEvent = 3;
        Rejection rejection = 4;
    }
}

// Interface exported by the events server
service Events {
    // event chatting using Event
    rpc Chat(stream Event) returns (stream Event) {}
}

```