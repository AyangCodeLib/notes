```java
NewTopic newTopic = new NewTopic(param.getTopic(), param.getNumPartitions(), param.getReplicationFactor());
Map<String, String> configs = new HashMap<>();
// 计算消息保留时间
String retentionTime = getRetentionTime(param.getRetentionTime());
// 设置消息保留时间
configs.put(TopicConfig.RETENTION_MS_CONFIG, retentionTime);
// 设置segment创建时间
configs.put(TopicConfig.SEGMENT_MS_CONFIG, retentionTime);
// 设置消息时间戳类型
configs.put(TopicConfig.MESSAGE_TIMESTAMP_TYPE_CONFIG, param.getTimestampType());
newTopic.configs(configs);
CreateTopicsResult topics = CLIENT.createTopics(Collections.singletonList(newTopic));
// 异步获取创建结果
topics.all().whenComplete((v, throwable) -> {
    if (Objects.nonNull(throwable)) {
        throwable.printStackTrace();
        log.info("topic: {}创建失败", param.getTopic());
    } else {
        log.info("topic: {} 创建成功", param.getTopic());
    }
});
```
其中`name`表示Topic的名称、`numPartitions`表示分区数、`repliactionFactor`表示副本数、`retentionTime`表示保留时间（时间单位是毫秒）、`TimeStampType`表示消息时间戳类型、`segmentMs`表示segment创建时间间隔设置和消息保留时间一致用于防止低流量Topic清理策略执行失败的情况。
> 更多的参数可以上TopicConfig类中进行查看并设置

