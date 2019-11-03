##Start Zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties 

##Start Kafka Server
./bin/kafka-server-start.sh config/server.properties

##List topis
./kafka-topics.sh --zookeeper 127.0.0.1:2181 --list
