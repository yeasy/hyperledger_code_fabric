## fabric.proto
十分核心的一个协议定义。

围绕着 fabric 主要功能相关的包括交易、块、节点一系列的消息和接口。

最核心的定义包括如下几个。

### Transaction
一个交易。

```
message Transaction {
    enum Type {
        UNDEFINED = 0;
        // deploy a chaincode to the network and call `Init` function
        CHAINCODE_DEPLOY = 1;
        // call a chaincode `Invoke` function as a transaction
        CHAINCODE_INVOKE = 2;
        // call a chaincode `query` function
        CHAINCODE_QUERY = 3;
        // terminate a chaincode; not implemented yet
        CHAINCODE_TERMINATE = 4;
    }
    Type type = 1;
    //store ChaincodeID as bytes so its encrypted value can be stored
    bytes chaincodeID = 2;
    bytes payload = 3;
    bytes metadata = 4;
    string uuid = 5;
    google.protobuf.Timestamp timestamp = 6;

    ConfidentialityLevel confidentialityLevel = 7;
    string confidentialityProtocolVersion = 8;
    bytes nonce = 9;

    bytes toValidators = 10;
    bytes cert = 11;
    bytes signature = 12;
}
```

### Block
一个区块。

```
message Block {
    uint32 version = 1;
    google.protobuf.Timestamp timestamp = 2;
    repeated Transaction transactions = 3;
    bytes stateHash = 4;
    bytes previousBlockHash = 5;
    bytes consensusMetadata = 6;
    NonHashData nonHashData = 7;
}
```

每个区块记录着前一个区块的哈希值。除此之外，还记录着两个重要信息：该区块收录的所有交易、依次执行完这些交易后的世界观的哈希值（即 stateHash，如存储世界观的默克尔树的根节点的哈希值）。
NonHashData 用来记录本地节点区块加入区块链的时间戳、区块中交易的结果等等，不属于区块被哈希的部分，并不要求在所有节点上一致。

### BlockchainInfo
区块链信息。

```
message BlockchainInfo {

    uint64 height = 1;
    bytes currentBlockHash = 2;
    bytes previousBlockHash = 3;

}
```

### Peer 之间传递的消息类型

```
message Message {
    enum Type {
        UNDEFINED = 0;

        DISC_HELLO = 1;
        DISC_DISCONNECT = 2;
        DISC_GET_PEERS = 3;
        DISC_PEERS = 4;
        DISC_NEWMSG = 5;

        CHAIN_TRANSACTION = 6;

        SYNC_GET_BLOCKS = 11;
        SYNC_BLOCKS = 12;
        SYNC_BLOCK_ADDED = 13;

        SYNC_STATE_GET_SNAPSHOT = 14;
        SYNC_STATE_SNAPSHOT = 15;
        SYNC_STATE_GET_DELTAS = 16;
        SYNC_STATE_DELTAS = 17;

        RESPONSE = 20;
        CONSENSUS = 21;
    }
    Type type = 1;
    google.protobuf.Timestamp timestamp = 2;
    bytes payload = 3;
    bytes signature = 4;
}
```

其中，payload 包含的对象取决于消息的类型。比如，如果消息类型是 CHAIN_TRANSACTION，那么 payload 就是一个 Transaction 对象。

### Peer 提供的服务

```
// Interface exported by the server.
service Peer {
    // Accepts a stream of Message during chat session, while receiving
    // other Message (e.g. from other peers).
    rpc Chat(stream Message) returns (stream Message) {}

    // Process a transaction from a remote source.
    rpc ProcessTransaction(Transaction) returns (Response) {}

}
```