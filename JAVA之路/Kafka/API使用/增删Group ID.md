# 创建Group ID
创建Group ID有两种方法，我目前使用的是第一种方法，第二种方法是在网上找到的，因为其中涉及到账号和权限等问题所有没有测试过是否好用。后面在对kafka进行整体学习和了解后进行尝试。
## 方法一：创建消费者创建消费者组
`这个方法创建消费者组时通过创建消费者连接当前的kafka的group id来创建消费组的`。
```java
private Map<String, Object> getConfigMap() {
    // consumer配置
    Map<String, Object> configMap = new HashMap<>();
    // 采用手动提交的方式
    configMap.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, false);
    configMap.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
    configMap.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
    return configMap;
}

/**
     * 新增
     *
     * @param param
     * @author shy
     * @Date 2024-05-07
     */
@Override
public void createGroup(Long clusterId, KafkaClusterParam param) {
    KafkaClient kafkaClient = kafkaClientService.getById(clusterId);
    if (Objects.isNull(kafkaClient)) {
        throw new ServiceException(401, "集群不存在");
    }
    Map<String, Object> configMap = this.getConfigMap();
    configMap.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, 10000);
    configMap.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, 1000);
    configMap.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "latest");
    configMap.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, kafkaClient.getBootstrapServers());
    configMap.put(ConsumerConfig.GROUP_ID_CONFIG, param.getClusterName());
    try (KafkaConsumer<String, String> consumer = new KafkaConsumer<>(configMap);) {
        consumer.subscribe(Collections.singletonList(CREATE_GROUP_TOPIC));
        consumer.poll(Duration.ofMillis(1000));
        addConsumerGroup(clusterId, param);
    }catch (Exception e){
        throw new ServiceException(401, "创建消费者组失败");
    }

}
```

- 先编写了一个私有方法，用于创建基本面的消费者配置（因为功能还有获取当前消费组中消费者的消费进度计算功能需要创建相同的消费者所以把相同的代码块拉出来）。
- 其中主要配置了通过手动提交的方式（因为我只是创建消费者组不需要消费消息）和设置了对应的解码方法。
- 后续配置添加上对应偏移量的读取方式，是从最新的开始以及设置了Group ID。
- 然后使用try的方式管理连接，建立连接后设置后去消息并配置了一秒的超时时间

这样获取玩消息后直接关闭消费者就创建了一个消费者组。
## 方法二：账号绑定消费组创建消费组
创建Group ID也是属于[Kafka](https://so.csdn.net/so/search?q=Kafka&spm=1001.2101.3001.7020) 权限一系列的操作，因此我们还是使用的还是adminClient.createAcls()这个方法
```java
public void createGroup(String groupId) throws ExecutionException, InterruptedException {
    //创建Group对象
    ResourcePattern resourcePattern = new ResourcePattern(ResourceType.GROUP, groupId, PatternType.LITERAL);
    //创建账户对象
    AccessControlEntry accessControlEntry=new AccessControlEntry("User:kaf_java_int","*", AclOperation.ALL, AclPermissionType.ALLOW);
    //绑定对象和Group对象
    AclBinding aclBinding=new AclBinding(resourcePattern,accessControlEntry);
    Collection<AclBinding> aclBindingCollection= new ArrayList<>();
    //添加到列表
    aclBindingCollection.add(aclBinding);
    //给账户绑定上这个Group
    CreateAclsResult aclResult = adminClient.createAcls(aclBindingCollection);
    KafkaFuture<Void> result = aclResult.all();
    result.get();
    if (result.isDone()){
        System.out.println("createGroup:"+result.toString());
    }
}

```
# 删除Group ID
删除Group ID的方法也是有两个，第一个是我现在在用的，第二个同样涉及到了账户权限问题。
## 方法一：deleteConsumerGroups()
通过使用AdminClient的deleteConsumerGroups方法来删除消费者组
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1715308273801-5704bb2d-2467-4cf6-9c05-fe6181da9b83.png#averageHue=%232d2c2c&clientId=u7800d125-cd59-4&from=paste&height=287&id=u23eefc7f&originHeight=287&originWidth=1104&originalType=binary&ratio=1&rotation=0&showTitle=false&size=45031&status=done&style=none&taskId=ubd4af488-aa3d-4804-912a-cdf08c902bf&title=&width=1104)
共有两个方法，主要是使用下面那一个。通过传递一个Group ID 的集合来完成删除消费者组。
```java
private static void deleteConsumerGroup(AdminClient client, String groupId) {
    DeleteConsumerGroupsResult deleteConsumerGroups = client.deleteConsumerGroups(Arrays.asList(groupId));
    try {
        deleteConsumerGroups.all().get();
        // 只要不报错就表示删除正常
    } catch (InterruptedException | ExecutionException e) {
        e.printStackTrace();
    }
```
## 方法二：
删除操作同样要使用ACL的相关方法adminClient.deleteAcls()去做
```java
public void removeGroupId(String groupId) throws ExecutionException, InterruptedException {
    //创建Group对象
    ResourcePatternFilter resourcePatternFilter = new ResourcePatternFilter(ResourceType.GROUP, groupId, PatternType.LITERAL);
    //创建账户对象
    AccessControlEntryFilter accessControlEntry = new AccessControlEntryFilter("User:kaf_java_int", "*", AclOperation.ALL, AclPermissionType.ALLOW);
    //绑定对象和Group对象
    AclBindingFilter aclBindingFilter = new AclBindingFilter(resourcePatternFilter, accessControlEntry);
    Collection<AclBindingFilter> aclBindingCollection = new ArrayList<>();
    //添加到列表
    aclBindingCollection.add(aclBindingFilter);
    //从账户上移除Group ID
    DeleteAclsResult result = adminClient.deleteAcls(aclBindingCollection);
    result.all().get();
    if (result.all().isDone()){
        System.out.println("done");
    }
}
```
但是要注意的是，上面你给Group ID绑定了什么权限，这里最好移除同样的权限，就是AclOperation.ALL这里的配置，笔者上下都是配置的ALL，因为Group ID其实并不能做什么，配置ALL权限可以在使用中减少很多的麻烦。由于这是一个emun类型，因此你可以指定任何想要配置的权限，比如可以任意指定下列13种权限中的任意一种：
```java
public enum AclOperation {
	UNKNOWN((byte) 0), 
	ANY((byte) 1), 
	ALL((byte) 2), 
	READ((byte) 3), 
	WRITE((byte) 4), 
	CREATE((byte) 5), 
	DELETE((byte) 6), 
	ALTER((byte) 7), 
	DESCRIBE((byte) 8), 
	CLUSTER_ACTION((byte) 9), 
	DESCRIBE_CONFIGS((byte) 10), 
	ALTER_CONFIGS((byte) 11),
	IDEMPOTENT_WRITE((byte) 12);
...code...
}
```

