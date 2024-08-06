# 修改Topic的参数2.1.x版本
这个版本的参数修改还比较原始，当执行修改以后，会发现并不是修改而是全部覆盖，会把原来的参数给刷新掉，导致只有新的参数生效，其余参数都会被刷新为默认参数。因此如果要添加或者重新设置某一个值，最好的做法就是先拿到目标参数的值（参考上面代码），然后传递进来批量更新。笔者的`Sample`仅仅对更新做一个例子，具体项目中要如何使用，还需要各位自定。首先还是要介绍一下API中提供的方法alterConfigs(Map<ConfigResource,Config> configs)，这个方法理论上可以用于修改2.0.x 到 2.2.x以下的相关参数。
![](https://cdn.nlark.com/yuque/0/2024/png/40608915/1714961093323-b589dafb-d122-4cf4-9b27-93787b6c2877.png#averageHue=%23464d45&clientId=u4ef08b69-a2d8-4&from=paste&id=u3e8c0de9&originHeight=134&originWidth=1362&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u0b38a367-72cf-4721-8da8-0ed5555ec66&title=)
可以看到执行修改以后，修改的参数也是被封装到AlterConfigsResult类中，但是由于我们只要知道结果就可以了，所以这个类并不是很重要。从其传入的参数来看我们首先要构建的除了ConfigResource外，还要构造一个Config类。
## Config和ConfigEntry
首先看下Config如何构建，官网上关于它的构造方法如下：
![](https://cdn.nlark.com/yuque/0/2024/png/40608915/1714961173013-1c318bb1-ec2a-4cb4-84fb-4e7d8729233f.png#averageHue=%23db983f&clientId=u4ef08b69-a2d8-4&from=paste&id=u353a5c9b&originHeight=152&originWidth=1005&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u67a2a9b9-6fe3-4f63-a2b4-9b2242f2f52&title=)
由官网所知构建Config类之前，先要去构建ConfigEntry类，同样去官网看下这个类是什么样子的：
![](https://cdn.nlark.com/yuque/0/2024/png/40608915/1714961232925-a662d22f-496b-43d0-a366-ca0c3be7ff93.png#averageHue=%23d8973f&clientId=u5e5f01ab-c45a-4&from=paste&id=u1f8f5bca&originHeight=155&originWidth=1002&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u021b4c14-9358-4b0c-af7a-a66e5ad4fea&title=)
发现这个类的构造方法的参数是两个String字符串，而且根据官方介绍来看，这里传入的应该就是参数的名字和值。那么也就是说我们要把参数写到这个类里，然后做一个集合传递给Config。再由ConfigResource和Config一起组成Map交给alterConfigs()方法调用即可。
## Sample
```java
public void updateTopicConfig210() throws ExecutionException, InterruptedException {
	//构建Map<ConfigResource, Config>为传入准备
    Map<ConfigResource, Config> configs=new HashMap<>();
    //声明一个ConfigResource，用来判断是给什么用的，此处是给topic，名字是java_test用的
    ConfigResource configResource=new ConfigResource(ConfigResource.Type.TOPIC,"java_tst");
    //声明一个ConfigEntry作为配置入口，设置为添加gzip的压缩方式
    ConfigEntry configEntry=new ConfigEntry("compression.type", "gzip");
    //声明一个ConfigEntry作为配置入口，设置为添加compact的清除策略
    ConfigEntry configEntry2=new ConfigEntry("cleanup.policy", "compact");
    //添加到Collection中用来传入Config类
    Collection<ConfigEntry> configEntries=new ArrayList<>();
    configEntries.add(configEntry);
    configEntries.add(configEntry2);
    //设置一个Config对象用来，处理做什么操作
    Config config=new Config(configEntries);
    //给map赋值
    configs.put(configResource,config);
    //调用方法
    AlterConfigsResult result = adminClient.alterConfigs(configs);
    System.out.println(result.all().get());
    if (result.all().isDone()){
        System.out.println("done");
    }
}
```
# 修改Topic的参数2.5.x版本
说完2.1.x版本以后，一起来看看2.5.x版本有了什么变化。一个很明显的变化就是`alterConfigs(Map<ConfigResource,Config> configs)`方法过期了，取而代之的是`incrementalAlterConfigs(Map<ConfigResource,Collection<AlterConfigOp>> configs)`。值得注意的是`incrementalAlterConfigs()`方法对低于2.3.x版本是无效的。
![](https://cdn.nlark.com/yuque/0/2024/png/40608915/1714961569698-1e397962-18e2-49e5-820d-8b0ccbc82bf4.png#averageHue=%23538327&clientId=u5e5f01ab-c45a-4&from=paste&id=uc6c0c3bc&originHeight=140&originWidth=1509&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=ubdc6fdfb-5ba0-4b46-b1fd-cc81ec01795&title=)
官网也同样在原来的方法上添加了注释：
![](https://cdn.nlark.com/yuque/0/2024/png/40608915/1714961583558-811426f4-00fd-4eaa-b9bc-f0c49511bfb9.png#averageHue=%234b5249&clientId=u5e5f01ab-c45a-4&from=paste&id=u7024af8c&originHeight=191&originWidth=1257&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u5b29e1f6-bad9-4618-bda4-77e2ea90964&title=)
2.3.x版本以后就建议使用incrementalAlterConfigs()方法，那么下面我们就看下这个方法有什么不一样。首先还是要从入参开始，这次的入参就变成了一个ConfigResource，另外一个是AlterConfigOp的集合。
## AlterConfigOp
碰见了一个新面孔，还是要先去官网上看一下，如何构建一个AlterConfigOp类实例：
![](https://cdn.nlark.com/yuque/0/2024/png/40608915/1714961706606-b6e82d43-d91c-46c6-b5ba-948836ab00b4.png#averageHue=%23dc973c&clientId=u5e5f01ab-c45a-4&from=paste&id=ucd10a1ca&originHeight=130&originWidth=773&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u4cac2502-7337-4166-9a12-f277cafc3c8&title=)
构建它需要两个参数，ConfigEntry已经说过，AlterConfigOp.OpType是啥呢？这是一个enum类型，用来限定这个配置的操作是什么，它有四个值：

1. APPEND ：添加ConfigEntry给目标Topic。
2. DELETE ：删除ConfigEntry给目标Topic。
3. SET ：设置ConfigEntry给目标Topic。
4. SUBTRACT ：减少ConfigEntry给目标Topic。

但是笔者并没有测出APPEND和SET有什么具体的区别，同样DELETE和SUBTRACT似乎效果也是一样的。遗憾的是官网也没有给更多的解释，姑且认为官方有别的想法吧。既然已经知道如何构建了，那么就开始写代码吧。
## Sample
```java
public void alterConfigurationTest250() throws ExecutionException, InterruptedException {
    Map<ConfigResource, Collection<AlterConfigOp>> configs =new HashMap<>();
    //声明一个ConfigResource，用来判断是给什么用的，此处是给topic，名字是java_test用的
    ConfigResource configResource=new ConfigResource(ConfigResource.Type.TOPIC,"java_tst");
    //声明一个ConfigEntry作为配置入口，设置为添加gzip的压缩方式
    ConfigEntry configEntry=new ConfigEntry("compression.type", "gzip");
    //设置一个AlterConfigOp对象用来，处理做什么操作，这里使用的SET(或者APPEND)类型的添加操作
    AlterConfigOp alterConfigOp=new AlterConfigOp(configEntry,AlterConfigOp.OpType.SET);
    //如果要删除，改为下面的DELETE操作(或者SUBTRACT)即可
    AlterConfigOp alterConfigOp=new AlterConfigOp(configEntry,AlterConfigOp.OpType.DELETE);
    //添加到Collection中，用于传到incrementalAlterConfigs方法中
    Collection<AlterConfigOp> configOps=new ArrayList<>();
    configOps.add(alterConfigOp);
    //给map赋值
    configs.put(configResource,configOps);
    //调用方法
    AlterConfigsResult result = adminClient.incrementalAlterConfigs(configs);
    System.out.println(result.all().get());
    if (result.all().isDone()){
        System.out.println("done");
    }
}
```
## 扩展
```java
public enum OpType {
        /**
         * Set the value of the configuration entry.
         */
        SET((byte) 0),
        /**
         * Revert the configuration entry to the default value (possibly null).
         */
        DELETE((byte) 1),
        /**
         * (For list-type configuration entries only.) Add the specified values to the
         * current value of the configuration entry. If the configuration value has not been set,
         * adds to the default value. */
        APPEND((byte) 2),
        /**
         * (For list-type configuration entries only.) Removes the specified values from the current
         * value of the configuration entry. It is legal to remove values that are not currently in the
         * configuration entry. Removing all entries from the current configuration value leaves an empty */
        SUBTRACT((byte) 3);
        
        ...code...
        
}
```
注释中可以看出APPEND和SUBTRACT这连个字段是为了list-type configuration entries，也就是说AlterConfigOp也可以识别List<ConfigEntry>类型的参数，截止到2.7.0版本的API中AlterConfigOp并没有提供这样一个构造方法，所以姑且认为这个是做为后续版本的扩展使用吧。KafkaAPI更新像飞一样，谁知道哪天就更新了呢。
# 修改分区数
```java
/**
   * 更新分区数
   * @param topic 主题名称
   * @param number 新分区数
*/
public void updatePartition(String topic, Integer number){
    Map<String, NewPartitions> newPartitions = new HashMap<>();
    // 给map存入Topic名称和扩展的分区数量（只可增加）
    newPartitions.put(topic, NewPartitions.increaseTo(number));
    // 调用
    CreatePartitionsResult result = CLIENT.createPartitions(newPartitions);
    if (!result.all().isDone()){
        throw new ServiceException(401, "更新分区数失败");
    }
}
```
