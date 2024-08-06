# 快速配置
如果这个主题有程序还在消费着，那么此时Kafka就game over了

1. kafka启动之前，在server.properties配置delete.topic.enable=true
2. 执行命令bin/kafka-topic.sh --delete --topic test --zookeeper zk:2181

注意：如果kafka启动之前没有配置delete.topic.enable=true，topic只会标记marked for deletion，加上配置，重启kafka，之前的topic就真正的删除了。
# 设置删除策略
如果这个主题有程序还在消费着，那么此时Kafka就game over了
kafka启动之前，在server.properties配置
```shell
#日志清理策略选择有：delete和compact主要针对过期数据的处理，或是日志文件达到限制的额度，会被 topic创建时的指定参数覆盖
log.cleanup.policy = delete
 
# 注意:下面有两种配置，一种是基于时间的策略，另种是基于日志文件大小的策略，两种策略同是配置的话，只要满足其中种策略，则触发Log删除的操作。删除操作总是先删除最旧的日志
# 消息在Kafka中保存的时间，168小时之前的1og， 可以被删除掉，根据policy处理数据。
log.retention.hours=4
 
# 当剩余空间低于log.retention.bytes字节，则开始删除1og
log.retention.bytes=37580963840
 
# 每隔300000ms, logcleaner线程将检查一次，看是否符合上述保留策略的消息可以被删除
log.retention.check.interval.ms=1000
```
# 手动删除
如果这个主题有程序还在消费着，那么此时Kafka就game over了

1. 删除zk下面topic
```shell
启动bin/zkCli.sh
ls /brokers/topics
rmr /brokers/topics/test
ls /brokers/topics
查topic是否删除：bin/kafka-topics.sh --list --zookeeper zk:2181
```

2. 删除个broker下topic数据，默认目录为、tmp/kafka-logs
# 偏移量（使用）
```java
Map<TopicPartition, RecordsToDelete> recordsToDelete = new HashMap<>();
// 指定topic和partition
TopicPartition topicPartition = new TopicPartition(param.getTopic(), param.getPartition());
// 指定offset
RecordsToDelete recordsToDelete1 = RecordsToDelete.beforeOffset(param.getOffset());
recordsToDelete.put(topicPartition, recordsToDelete1);
// 执行
DeleteRecordsResult result = adminClient.deleteRecords(recordsToDelete);
Map<TopicPartition, KafkaFuture<DeletedRecords>> lowWatermarks = result.lowWatermarks();
try {
    for (Map.Entry<TopicPartition, KafkaFuture<DeletedRecords>> entry : lowWatermarks.entrySet()) {
        System.out.println(entry.getKey().topic() + " " + entry.getKey().partition() + " " + entry.getValue().get().lowWatermark());
    }
} catch (InterruptedException | ExecutionException e) {
    e.printStackTrace();
}
```
> 目前发现这种方式的效果为：
> topic的其实偏移量会被定位到传入的offset为止
> 但是数据并没有从磁盘中删除，这就需要配置log.segment.bytes和log.segment.ms参数来删除掉了。

