### kafka
启动一个 kafka 环境，包括 zookeeper 和 kakka。

#### 启动

```console
$ docker-compose up -d
Creating kafka_zookeeper_1
Creating kafka_kafka_1
$
```

#### 扩展到多个

```console
$ docker-compose scale kafka=3
Creating and starting kafka_kafka_2 ... done
Creating and starting kafka_kafka_3 ... done
$
```

#### 停止

```console
$ docker-compose stop
Stopping kafka_kafka_3 ... done
Stopping kafka_kafka_2 ... done
Stopping kafka_kafka_1 ... done
Stopping kafka_zookeeper_1 ... done
$
```