## server_admin.proto
对服务进行管理：目前包括状态查询和启停操作。

```
// Interface exported by the server.
service Admin {
    // Return the serve status.
    rpc GetStatus(google.protobuf.Empty) returns (ServerStatus) {}
    rpc StartServer(google.protobuf.Empty) returns (ServerStatus) {}
    rpc StopServer(google.protobuf.Empty) returns (ServerStatus) {}
}

message ServerStatus {

    enum StatusCode {
        UNDEFINED = 0;
        STARTED = 1;
        STOPPED = 2;
        PAUSED = 3;
        ERROR = 4;
        UNKNOWN = 5;
    }

    StatusCode status = 1;

}
```