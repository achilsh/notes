#1.启动kafka
```
cd kafka_2.11-0.9.0.0
bin/zookeeper-server-start.sh config/zookeeper.properties &
bin/kafka-server-start.sh config/server.properties &
```

#2.修改kafka server发送消息大小
```
cd kafka/
vim config/server.properties

增加选项,消息大小修改为10M
#This is largest message size Kafka will allow to be appended to this topic. Note that if you increase this size you must also increase your consumer's fetch size so they can fetch messages this large.
#max allow is (10MB)
message.max.bytes=10485760
replica.fetch.max.bytes=10485760

优化kafka server,增加内存
vim bin/kafka-server-start.sh

修改 export KAFKA_HEAP_OPTS="-Xmx1G –Xms1G"
为   export KAFKA_HEAP_OPTS="-Xmx2G -Xms2G"
```
#3.Adjust default values of log.retention.hours and offsets.retention.minutes
```
https://issues.apache.org/jira/browse/KAFKA-3806
```
#4.删除kafka主题
```
修改kafka的config/server.properties
添加delete.topic.enable=true

bin/kafka-topics.sh --delete --zookeeper zookeeper01:2181,zookeeper02:2181,zookeeper03:2181/kafka --topic LULU_TEST
```
#5.压测工具
```
#生产者压测工具
#--producer-props参考  http://kafka.apache.org/documentation#producerconfigs
bin/kafka-producer-perf-test.sh --topic TEST_01 --num-records 1000000 --record-size 1000 --producer-props bootstrap.servers=kafka01:9092,kafka02:9092 --throughput 100000

#消费者压测工具
bin/kafka-consumer-perf-test.sh --broker-list kafka01:9092,kafka02:9092 --zookeeper zookeeper01:2181,zookeeper02:2181/kafka --topic TEST_01 --messages 1000000 --message-size 1000 --group gid_test
```
