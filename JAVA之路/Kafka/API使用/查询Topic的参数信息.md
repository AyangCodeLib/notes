# 查询Topic的设置信息
在`Apache API`中给我们提供了一个（）`describeConfigs(Collection<ConfigResource resources>)`方法，用来查询Topic的参数，并用`DescribeConfigsResult`类来接受。
![](https://cdn.nlark.com/yuque/0/2024/png/40608915/1714381652228-87dac4ac-9e46-46bf-aca3-c3730c23800a.png#averageHue=%233f443d&clientId=u5e811761-c7db-4&from=paste&id=u5b879617&originHeight=135&originWidth=1449&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u226b57d6-cf8e-4942-9b93-f51e183fa64&title=)
首先先看参数是一个`ConfigResource`类的集合，顾名思义就是用来存放配置源的，那么第一步我们就要去构建这个`ConfigResource`，然后封装为一个集合，传递进去拿数据。
```java
public Map<String, String> readConfig() throws ExecutionException, InterruptedException {
	//map用来存放返回信息
    Map<String, String> returnMap=new HashMap<>();
    //构建ConfigResource，指定name为java_tst的Topic
    ConfigResource configResource=new ConfigResource(ConfigResource.Type.TOPIC,"java_tst");
    //构建集合并添加到configResource进入
    Collection<ConfigResource> configResources=new ArrayList<>();
    configResources.add(configResource);
    //执行方法拿到返回结果
    DescribeConfigsResult result = adminClient.describeConfigs(configResources);
    //等待返回结果完成
    result.all().get();
    //解析返回结果
    Map<ConfigResource, KafkaFuture<Config>> futureMap = result.values();
    Collection<KafkaFuture<Config>> list = futureMap.values();
    for (KafkaFuture<Config> future: list) {
        Collection<ConfigEntry> entry = future.get().entries();
        for (ConfigEntry ce:entry){
            System.out.println(ce.name());//参数的名字
            System.out.println(ce.value());//参数的值
            System.out.println(ce.isDefault());//此参数是不是默认参数
            returnMap.put(ce.name(),ce.value());//添加到返回map中
        }
    }
    if (result.all().isDone()){ //如果执行无误返回map
        return returnMap;
    }else
        return null;
}

```
# 查询Topic的分区数和副本数
```java
public class GetTopicAboutDatasource {
    public static void main(String[] args) {
        String kafka = "192.168.140.65:9092";
        String[] kafkas = kafka.split(";");
        for(int i=0;i<kafkas.length;i++){
            String[] _kafka = kafkas[i].split(":");
            if(_kafka.length<2){
                System.out.println("有个地址缺少IP或端口，获取topic失败");
            }
        }
        List<Map<String,String>> tVos = new ArrayList<>();
        List<String> list = new ArrayList<>();
        AdminClient adminiClient = KafkaConnection.kafkaTestConnection(kafka);
        ListTopicsOptions options = new ListTopicsOptions();
        options.listInternal(true);
        ListTopicsResult topicsResult = adminiClient.listTopics(options);
        try {
            Set<String> topicNames = topicsResult.names().get();
            Iterator it = topicNames.iterator();
            while (it.hasNext()){
                Map<String,String> map = new HashMap<>();
                String topicName = it.next().toString();
                Map topicInfo = getTopicInfo(kafka,topicName);
                map.put("tableName",topicName);
                map.put("issame","0");
                map.put("Partitions", String.valueOf(topicInfo.get("Partitions")));
                map.put("PartitionSize", String.valueOf(topicInfo.get("PartitionSize")));
                map.put("ReplicationFactor", String.valueOf(topicInfo.get("ReplicationFactor")));
                tVos.add(map);
            }
        } catch (Exception e) {
            System.out.println("获取topic失败");
        }finally {
            KafkaConnection.close(adminiClient);
        }
        System.out.println("所有信息查询成功tVos："+tVos);
        tVos.stream().forEach(maptopic -> {
            System.out.println("————————————————————————————————————");
            System.out.println("topic主题名称："+maptopic.get("tableName"));
            maptopic.get("issame");
            maptopic.get("Partitions");
            System.out.println("topic分区信息："+maptopic.get("Partitions"));
            maptopic.get("PartitionSize");
            System.out.println("topic分区数量："+maptopic.get("PartitionSize"));
            maptopic.get("ReplicationFactor");
            System.out.println("topic副本数量："+maptopic.get("ReplicationFactor"));
            System.out.println("————————————————————————————————————");
        });
    }
    //获取topic的详细信息
    public static Map getTopicInfo(String ipAndPort,String topic){
        Map<String, Object> map = new HashMap<>();
        Properties props = new Properties();
        props.put(AdminClientConfig.BOOTSTRAP_SERVERS_CONFIG, ipAndPort);
 
        AdminClient adminClient = AdminClient.create(props);
        String topicName = topic;
        DescribeTopicsOptions describeTopicsOptions = new DescribeTopicsOptions().timeoutMs(5000);
 
        DescribeTopicsResult describeTopicsResult = adminClient.describeTopics(Arrays.asList(topicName), describeTopicsOptions);
        KafkaFuture<TopicDescription> topicDescriptionFuture = describeTopicsResult.values().get(topicName);
 
        try {
            TopicDescription topicDescription = topicDescriptionFuture.get();
            List<TopicPartitionInfo> partitions = topicDescription.partitions();
            int replicationFactor = partitions.get(0).replicas().size();
            map.put("Partitions", partitions);
            map.put("PartitionSize", partitions.size());
            map.put("ReplicationFactor", replicationFactor);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
        return map;
    }
 
}
```
