```java
Properties props = new Properties();
props.put("bootstrap.servers", BOOTSTRAP_SERVERS);
AdminClient adminClient = AdminClient.create(props);
```
其中`BOOTSTRAP_SERVERS`可以是个字符串数组有多个地址用于连接集群。
