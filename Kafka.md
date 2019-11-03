## Basic Commands
#### Start Zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties 

#### Start Kafka Server
./bin/kafka-server-start.sh config/server.properties

#### List topis
./kafka-topics.sh --zookeeper 127.0.0.1:2181 --list

#### Create topics
/kafka-topics.sh --zookeeper 127.0.0.1:2181 --topic first_topic --create --partitions 3 --replication-factor 1

#### Console Producer
./kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic first_topic --producer-property acks=all

#### Console Consumer
./kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic first_topic --from-beginning

#### Console Consumer group
./kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic first_topic --group first_app --from-beginning

./kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list

./kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group first_app --describe
