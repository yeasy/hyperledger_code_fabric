### orderer-n-kafka-n

启动一个可扩展的服务，包括 zookeeper、若干个 fabric-order 和若干个 kafka 节点。

如，启动 3 个 order 节点和 5 个 kafka 节点。

```bash
$ docker-compose up -d zookeeper
$ docker-compose up -d kafka
$ docker-compose scale kafka=5
$ docker-compose up -d orderer
$ docker-compose scale orderer=3
```