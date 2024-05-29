### Start Zookeeper
```./bin/zookeeper-server-start.sh config/zookeeper.properties```

### Start Kafka
```./bin/kafka-server-start.sh config/server.properties```

### Create Topic
```./bin/kafka-topics.sh --bootstrap-server localhost:9092 --topic hello_world --create --partitions 3 --replication-factor 1```

### List Topics
```./bin/kafka-topics.sh --bootstrap-server localhost:9092 --list```

### Describe Topic
```./bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic hello_world```

### Start Kafka Console Producer
```./bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic hello_world```

### Start Kafka Console Consumer
```./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic hello_world```
