根据下述方法可以查询出当前集群中的所有Group ID的名字有什么，可以根据获取到的消费组列表来获取对应消费组的信息
[查询所有Group ID](https://www.yuque.com/ayangnulixiulian/yh7nz6/tf6ifccrwpydithn?view=doc_embed)
官方给出了两个方法：

| Modeifier and Type |  Method | Description |
| --- | --- | --- |
| DescribeConsumerGroupsResult | describeConsumerGroups(Collection<String> groupIds) | Describe some group IDs in the cluster, with the default options. |
| abstract DescribeConsumerGroupsResult | ·describeConsumerGroups(Collection<String> groupIds,
DescribeConsumerGroupsOptions options) | Describe some group IDs in the cluster. |

主要方法就是`describeConsumerGroups()`，参数也是简单明了，需要传递一个Group ID的集合进去。
而且可以看得出来这个方法是支持多个Group ID查询的，传递几个Group ID进去就可以查出几个的信息。
# DescribeConsumerGroupsResult
看一下他返回的类型都有什么参数
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1715305268124-c8cd9ec2-4dd9-4f22-a7d4-8991dcba1bf9.png#averageHue=%232d2c2b&clientId=ued558d54-6f37-4&from=paste&height=406&id=u2c2eb700&originHeight=406&originWidth=924&originalType=binary&ratio=1&rotation=0&showTitle=false&size=63678&status=done&style=none&taskId=u16f41cf5-861d-4440-a867-e30306780f9&title=&width=924)
可以看到他返回的参数是个map，key是string类型的，根据逻辑应该是我们需要查询的Group ID名称，后面就是对应Group ID的信息了。
# ConsumerGroupDescription
看一下`ConsumerGroupDescription`类的参数信息
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1715305350452-8158a8fa-e27e-469d-82dd-704febabdf34.png#averageHue=%232f2d2b&clientId=ued558d54-6f37-4&from=paste&height=351&id=uea8db366&originHeight=351&originWidth=700&originalType=binary&ratio=1&rotation=0&showTitle=false&size=53226&status=done&style=none&taskId=ub74c6674-c829-4164-afde-9482a6d9846&title=&width=700)
可以看到其中主要的有：

- Group ID：消费组名称
- isSimpleConsumerGroup：是否被使用过
- members：消费组内的消费者信息
- partitionAssignor：分区分配器
- state：消费组状态

[消费者组状态](https://www.yuque.com/ayangnulixiulian/yh7nz6/hz46ru9qfh9t2ove?view=doc_embed)

- coordinator：协调员id
# Simple
```java
public void describeSingleGroupID(ArrayList<String> groupIds) throws ExecutionException, InterruptedException {
    DescribeConsumerGroupsResult groupIdResult = adminClient.describeConsumerGroups(groupIds);
    Map<String, ConsumerGroupDescription> groupIdResultCollection = groupIdResult.all().get();
	//如果传入的是多个Group ID就能循环拿到多个输出
    for (String key: groupIdResultCollection.keySet()) {
        System.out.println("Group ID: "+groupIdResultCollection.get(key));
    }
}
输出Group ID没有被使用过：
Group ID: (groupId=javaint, isSimpleConsumerGroup=true, members=, partitionAssignor=, state=Dead, coordinator=10.87.149.65:9092 (id: 5 rack: null), authorizedOperations=[])

输出Group ID被使用过或者正在被使用：
Group ID: (groupId=javaint, isSimpleConsumerGroup=false, 
members=(memberId=consumer-4-01505d82-46af-4f0d-9386-81c4ea3b3114, groupInstanceId=null, clientId=consumer-4, host=10.111.33.168/10.111.33.168, assignment=(topicPartitions=topic1-1,topic1-0)),
	(memberId=consumer-2-7a794a20-02fe-4699-83c6-7e55dbbe361d, groupInstanceId=null, clientId=consumer-2, host=10.111.33.168/10.111.33.168, assignment=(topicPartitions=topic2-3,topic2-2)),
	(memberId=consumer-4-bc385da9-68ba-450e-80dc-00681f15aa18, groupInstanceId=null, clientId=consumer-4, host=10.111.33.167/10.111.33.167, assignment=(topicPartitions=topic1-3,topic1-2)),
	(memberId=consumer-2-24f31ad6-ca6f-4fe9-8f32-ce8a0eac83ee, groupInstanceId=null, clientId=consumer-2, host=10.111.33.167/10.111.33.167, assignment=(topicPartitions=topic2-1,topic2-0)), 
	partitionAssignor=range, state=Stable, coordinator=10.87.149.65:9092 (id: 1 rack: null), authorizedOperations=[])

```
可以看到指定Group ID的方法能够拿到的信息，即便这个Group ID从来没有被使用过，那么我们就可以针对这些信息通过Group ID对消费者进行监控。比如这里可以知道有多少个consumer线程，分别从哪个host上发起的消费，状态是什么，partition分配规则是什么，在哪个broker上存的等等内容都可以拿到。
