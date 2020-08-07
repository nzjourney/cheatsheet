### Kafka Rebalance

- Create reassign json file:
```json
{ "version": 1,
    "partitions":
    [
       { "topic":"topic-name","partition":0,"replicas":[1,0,2] },
       { "topic":"topic-name","partition":1,"replicas":[0,2,1] },
       { "topic":"topic-name","partition":2,"replicas":[2,1,0] }
    ]
}
```

- Rebalance partition and replication:
```sh
./bin/kafka-reassign-partitions.sh --bootstrap-server localhost:9092 --zookeeper localhost:2181 --reassignment-json-file increase-replication-factor.json --execute
```

- Verify partitions:
```sh
./bin/kafka-reassign-partitions.sh --bootstrap-server localhost5:9092 --zookeeper localhost:2181 --reassignment-json-file increase-replication-factor.json --verify
```