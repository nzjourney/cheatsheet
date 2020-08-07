### Kafka Topic

- Create topic:
```sh
$ ./bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --topic topic-name
```

- Create topic with partitions:
```sh
$ ./bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --topic topic-name --partitions 3
```

- Create topic with partitions and replication:
```sh
$ ./bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --topic topic-name --partitions 3 --replication-factor 3
```

- Modify topic partitions:
```sh
$ ./bin/kafka-topics.sh --alter --bootstrap-server localhost:9092 --topic topic-name --partitions 5
```

- Describe topic:
```sh
$ ./bin/kafka-topics.sh --describe --bootstrap-server localhost:9092 --topic topic-name
```

- List topic:
```sh
$ ./bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```