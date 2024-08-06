查询当前服务器上所有活跃的Group ID。这里特别强调是**活跃的**，因为这个方法无法查询到没有连接的Group ID的。
假设我们创建了一个Group ID：XXX。当XXX没有被Consumer的账户使用过时，是无法查询出来的，同时如果Group ID的state为dead的话也是查询不出来的。
查询所有活跃的Group ID的方法叫做`listConsumerGroups()`。

| Modeifier and Type | Method | Description |
| --- | --- | --- |
| ListConsumerGroupsResult | listConsumerGroups() | List the consumer groups available in the cluster with the default options.  |
| abstract ListConsumerGroupsResult | listConsumerGroups(ListConsumerGroupsOptions options) | List the consumer groups available in the cluster. |

官方文档中提供了两个方法，一个是正常用的，一个抽象的用于扩展。只需要用到第一个即可，方法的返回值`ListGroupsResult`一如既往，也没什么多余参数，稍微看一下直接上Sample。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1715304320347-ecbb00ad-4101-4e08-8589-206ba0023fdf.png#averageHue=%232f2d2c&clientId=u894f8c57-e02d-4&from=paste&height=191&id=u39cca7d0&originHeight=191&originWidth=639&originalType=binary&ratio=1&rotation=0&showTitle=false&size=29360&status=done&style=none&taskId=u625aa322-ac7e-4533-88cc-c2a9a7b7c0f&title=&width=639)
主要是含有一个`ConsumerGroupListing`类的列表
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1715304358796-2434fb29-42f4-416f-96bd-1f11a9cebacb.png#averageHue=%232e2c2b&clientId=u894f8c57-e02d-4&from=paste&height=320&id=u9af625b0&originHeight=320&originWidth=640&originalType=binary&ratio=1&rotation=0&showTitle=false&size=40596&status=done&style=none&taskId=u2eae063e-514c-4bcc-a952-c77690014bc&title=&width=640)
这个类直接含有groupId。
# Simple
```java
public void listAllGroupId() throws ExecutionException, InterruptedException {
    ListConsumerGroupsResult listResult = adminClient.listConsumerGroups();
    Collection<ConsumerGroupListing> listResultCollection = listResult.all().get();
    for (ConsumerGroupListing consumerGroupListing : listResultCollection) {
        System.out.println(consumerGroupListing.groupId());
    }
}

输出内容，只是简单的把所有当前使用的Group ID的名字输出了出来：
group1
group2
group3
group4
java_test
java_topic
……
```
