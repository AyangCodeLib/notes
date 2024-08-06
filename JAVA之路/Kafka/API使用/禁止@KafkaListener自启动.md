# 创建容器工厂
`@KafkaListener`的默认情况下，当项目启动的时候，监听器就开始工作。如果想要禁止监听器自动启动，首先需要定义一个不自动启动的监听容器工厂。
```java
@Configuration
public class KafkaInitialConfiguration {
 
    // 监听器工厂
    @Autowired
    private ConsumerFactory consumerFactory;
 
    // 监听器容器工厂(设置禁止KafkaListener自启动)
    @Bean
    public ConcurrentKafkaListenerContainerFactory delayContainerFactory() {
        ConcurrentKafkaListenerContainerFactory container =
                new ConcurrentKafkaListenerContainerFactory();
        container.setConsumerFactory(consumerFactory);
        //禁止自动启动
        container.setAutoStartup(false);
        return container;
    }
}
```
直接就集成kafka的默认容器工厂然后设置为禁止自动启动就好，这样就不需要去配置那些非我们需求但kafka必须得参数了。

然后将这个容器工厂的BeanName放到`@KafkaListener`注解的containerFactory属性里面。这样该监听器就不会自动启动了。
```java
@Component
public class KafkaConsumer {
    // 消费监听
    @KafkaListener(id = "myListener1", topics = {"topic1"},
            containerFactory = "delayContainerFactory")
    public void listen1(String data) {
        System.out.println("监听器收到消息：" + data);
    }
}
```
# 设置注解参数
KafkaListener注解具有一个名为autoStartup的属性，可以用于控制是否自动启动消费者。默认情况下，它的值为true，表示自动启动。如果将其设置为false，则消费者将不会自动启动。
```java
@KafkaListener(topics = "<Kafka主题>", autoStartup = "false")
public void receive(String message) {
    // 处理接收到的消息
}
```
