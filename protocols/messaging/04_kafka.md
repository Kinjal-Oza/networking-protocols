# Apache Kafka

## Overview
Apache Kafka is a distributed event streaming platform designed for high-throughput, fault-tolerant, real-time data streaming. It's particularly well-suited for building real-time streaming data pipelines and applications that adapt to data streams.

## Technical Details

### Core Components
1. **Topics**: Categories/feeds for storing messages
2. **Partitions**: Distributed, ordered logs within topics
3. **Brokers**: Kafka servers that store and serve data
4. **ZooKeeper/KRaft**: Coordination service for broker cluster
5. **Producers**: Applications that publish messages
6. **Consumers**: Applications that subscribe to topics
7. **Consumer Groups**: Groups of consumers that work together

### Protocol Characteristics
- Transport: TCP
- Default Ports: 9092 (Kafka), 2181 (ZooKeeper)
- Message Format: Binary
- Maximum Message Size: Configurable (default 1MB)
- Replication Factor: Configurable per topic
- Partition Count: Configurable per topic

## Implementation

### Basic Setup (Docker Compose)
```yaml
# docker-compose.yml
version: '3.8'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
    ports:
      - "2181:2181"

  kafka:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    ports:
      - "9092:9092"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
```

### Producer Example (Python with kafka-python)
```python
from kafka import KafkaProducer
import json

class KafkaMessageProducer:
    def __init__(self, bootstrap_servers=['localhost:9092']):
        self.producer = KafkaProducer(
            bootstrap_servers=bootstrap_servers,
            value_serializer=lambda v: json.dumps(v).encode('utf-8'),
            acks='all'
        )

    def send_message(self, topic, message, key=None):
        future = self.producer.send(
            topic,
            value=message,
            key=key.encode() if key else None
        )
        try:
            record_metadata = future.get(timeout=10)
            print(f"Message sent to topic {record_metadata.topic}")
            print(f"Partition: {record_metadata.partition}")
            print(f"Offset: {record_metadata.offset}")
        except Exception as e:
            print(f"Error sending message: {str(e)}")

    def close(self):
        self.producer.close()

# Usage example
producer = KafkaMessageProducer()
producer.send_message(
    'orders',
    {'order_id': '123', 'product': 'laptop'},
    key='order-123'
)
producer.close()
```

### Consumer Example (Python with kafka-python)
```python
from kafka import KafkaConsumer
import json

class KafkaMessageConsumer:
    def __init__(self, topics, group_id, bootstrap_servers=['localhost:9092']):
        self.consumer = KafkaConsumer(
            *topics,
            bootstrap_servers=bootstrap_servers,
            group_id=group_id,
            auto_offset_reset='earliest',
            enable_auto_commit=True,
            value_deserializer=lambda x: json.loads(x.decode('utf-8'))
        )

    def consume_messages(self):
        try:
            for message in self.consumer:
                print(f"Topic: {message.topic}")
                print(f"Partition: {message.partition}")
                print(f"Offset: {message.offset}")
                print(f"Key: {message.key}")
                print(f"Value: {message.value}")
                # Process message here
                yield message.value
        except Exception as e:
            print(f"Error consuming messages: {str(e)}")

    def close(self):
        self.consumer.close()

# Usage example
consumer = KafkaMessageConsumer(['orders'], 'order-processing-group')
for message in consumer.consume_messages():
    # Process message
    print(f"Processing order: {message}")
consumer.close()
```

## Design Considerations

### High Availability Configuration
```properties
# server.properties
broker.id=1
num.network.threads=3
num.io.threads=8
socket.send.buffer.bytes=102400
socket.receive.buffer.bytes=102400
socket.request.max.bytes=104857600
log.dirs=/var/lib/kafka/data
num.partitions=3
num.recovery.threads.per.data.dir=1
offsets.topic.replication.factor=3
transaction.state.log.replication.factor=3
transaction.state.log.min.isr=2
log.retention.hours=168
log.segment.bytes=1073741824
log.retention.check.interval.ms=300000
zookeeper.connect=zookeeper1:2181,zookeeper2:2181,zookeeper3:2181
```

### Performance Tuning
```properties
# server.properties
# Network
num.network.threads=8
num.io.threads=16
socket.send.buffer.bytes=1048576
socket.receive.buffer.bytes=1048576

# Topic defaults
num.partitions=8
default.replication.factor=3

# Log configuration
log.flush.interval.messages=10000
log.flush.interval.ms=1000
log.retention.bytes=1073741824
log.segment.bytes=536870912
```

## Security Implementation

### SSL Configuration
```properties
# server.properties
listeners=PLAINTEXT://localhost:9092,SSL://localhost:9093
ssl.keystore.location=/path/to/kafka.server.keystore.jks
ssl.keystore.password=keystore-password
ssl.key.password=key-password
ssl.truststore.location=/path/to/kafka.server.truststore.jks
ssl.truststore.password=truststore-password
ssl.client.auth=required
```

### SASL Configuration
```properties
# server.properties
listeners=SASL_SSL://localhost:9093
security.inter.broker.protocol=SASL_SSL
sasl.mechanism.inter.broker.protocol=PLAIN
sasl.enabled.mechanisms=PLAIN

# JAAS configuration
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
    username="admin" \
    password="admin-secret" \
    user_admin="admin-secret" \
    user_alice="alice-secret";
```

## Best Practices

### Implementation Guidelines
1. Choose appropriate partition count and replication factor
2. Use appropriate key distribution for partitioning
3. Configure proper retention policies
4. Implement proper error handling and retry logic
5. Monitor consumer lag

### Monitoring and Maintenance
1. **Broker Monitoring**
   ```bash
   # Check topic details
   kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic my-topic
   
   # Check consumer groups
   kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group my-group
   
   # Check cluster health
   kafka-topics.sh --bootstrap-server localhost:9092 --describe
   ```

2. **Performance Metrics**
   - Producer/Consumer throughput
   - Broker disk usage
   - Network throughput
   - Consumer lag
   - Request latency

### Error Handling
1. Implement retry mechanisms
2. Use dead letter queues
3. Monitor consumer offsets
4. Handle partition reassignments
5. Implement proper logging

## Interview Tips

### Key Concepts to Understand
1. Partitioning and replication
2. Consumer groups and offset management
3. Message delivery semantics
4. Scaling and performance considerations
5. Fault tolerance mechanisms

### Common Questions
1. How does Kafka ensure message durability?
2. What is the role of ZooKeeper/KRaft in Kafka?
3. How does Kafka handle scalability?
4. What are the different delivery guarantees in Kafka?
5. How to handle message ordering in Kafka?

### Real-world Scenarios
1. Building real-time analytics pipeline
2. Implementing event sourcing
3. Creating data integration solutions
4. Building stream processing applications
5. Implementing log aggregation systems 