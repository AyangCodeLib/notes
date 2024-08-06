# KafkaListenerEndpointRegistry
在springboot-kafka的api中提供了一个注解`@KafkaListener`，使用该注解我们就可以编辑一个方法来直接消费监听到的消息了。下方是个简易例子：
```java
@Component
public class KafkaConsumer {
    // 消费监听
    @KafkaListener(id = "myListener1", topics = {"topic1"})
    public void listen1(String data) {
        System.out.println("监听器收到消息：" + data);
    }
}
```
设置了消费者ID（监听器ID），后面是根据这个ID来进行动态开启、关闭监听的。

然后可以定义两个http接口分别通过`KafkaListenerEndpointRegistry`来控制监听器的开启和关闭：
:::info
注意：

- KafkaListtenerEndpointRegistry在SrpingIO中已经被注册为Bean，直接注入使用即可。不过他是隐式注入
:::
## 开启
要在运行时动态启动消费者，你可以通过KafkaListenerEndpointRegistry bean来手动启动：
```java
@Autowired
private KafkaListenerEndpointRegistry endpointRegistry;

// 启动消费者
endpointRegistry.getListenerContainer("<KafkaListener的bean名称>").start();
```
## 关闭
同样，你也可以使用stop()方法来停止消费者：
```java
// 停止消费者
endpointRegistry.getListenerContainer("<KafkaListener的bean名称>").stop();
```
## 暂停和恢复
```java
@Autowired
private KafkaListenerEndpointRegistry endpointRegistry;

// 暂停消费者监听
endpointRegistry.getListenerContainer("<KafkaListener的bean名称>").pause();

// 恢复消费者监听
endpointRegistry.getListenerContainer("<KafkaListener的bean名称>").resume();
```
## Sample
```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.config.KafkaListenerEndpointRegistry;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

 
@Slf4j
@RestController
public class KafkaConsumerController {

    @Autowired
    private KafkaListenerEndpointRegistry registry;

    /**
     * 开启监听
     */
    @GetMapping("/start")
    public void start() {
        // 判断监听容器是否启动，未启动则将其启动
        if (!registry.getListenerContainer("attackConsumer").isRunning()) {
            log.info("start  ");

            registry.getListenerContainer("attackConsumer").start();
        }
        // 将其恢复
        registry.getListenerContainer("attackConsumer").resume();

        log.info("resume over ");
    }

    /**
     * 关闭监听
     */
    @GetMapping("/pause")
    public void pause() {
        // 暂停监听
        registry.getListenerContainer("attackConsumer").pause();

        log.info("pause");
    }
}
```
通过上述步骤就可以实现借助kafka的监听端点注册表来完成动态的开关kafka消费者了。
如果想禁止这个注解的自启动可以参考下方文章
[禁止@KafkaListener自启动](https://www.yuque.com/ayangnulixiulian/yh7nz6/vcz0xkfb24bz6glz?view=doc_embed)
# applicationContext
## 实现动态创建
在很多情况下我们不能提前知道一个消费者需要消费的topic是哪个，我们没法去提前定义好KafkaListener监听来启动监听，所以需要一种方法来实现动态的创建消费者。
```java
private static Map<String, Object> getConfigMap(String groupId) {
    // consumer配置
    Map<String, Object> configMap = new HashMap<>();
    // 采用手动提交的方式
    configMap.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, false);
    configMap.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, 10000);
    configMap.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
    configMap.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, 1000);
    configMap.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, BOOTSTRAP_SERVERS);
    configMap.put(ConsumerConfig.GROUP_ID_CONFIG, groupId);
    return configMap;
}
```
```java
public KafkaMessageListenerContainer<String, String> createConsumer(String topic, String groupId, CustomerMsgHandler customerMsgHandler) {
    // consumer配置
    Map<String, Object> configMap = getConfigMap(groupId);
    // kafka监听器
    Deserializer<String> stringDeserializer = new StringDeserializer();
    DefaultKafkaConsumerFactory<String, String> factory = new DefaultKafkaConsumerFactory<>(configMap, stringDeserializer, stringDeserializer);
    ContainerProperties props = new ContainerProperties(topic);
    props.setMessageListener(customerMsgHandler);
    props.setGroupId(configMap.get(ConsumerConfig.GROUP_ID_CONFIG).toString());
    props.setAckMode(ContainerProperties.AckMode.MANUAL);
    KafkaMessageListenerContainer<String, String> container = new KafkaMessageListenerContainer<>(factory, props);
    container.setBeanName(groupId);

    //将kafka监听容器添加到应用上下文中
    ConfigurableApplicationContext configurableApplicationContext = (ConfigurableApplicationContext) applicationContext;
    configurableApplicationContext.getBeanFactory().registerSingleton(groupId, container);
    return container;
}
```
```java
@ChannelHandler.Sharable
public class CustomerMsgHandler implements AcknowledgingMessageListener<String, String> {

    private final KafkaService kafka;

    public CustomerMsgHandler(KafkaService kafka) {
        this.kafka = kafka;
    }


    @Override
    public void onMessage(ConsumerRecord<String, String> data, Acknowledgment acknowledgment) {
        String value = data.value();
        System.out.println(data);
        // 因为前面设置了手动提交ack的方式，这里需要在消息处理完成后提交ack
        //        acknowledgment.acknowledge();
    }
```
通过默认的kafka消费者工厂来创建一个监听容器，并将容器添加到应用上下文中。通过编写处理程序来完成消息的处理。
其中handler必须设置sharable注解，因为这个类需要被多个消费者使用，需要设置一下分享注解。
## 动态删除
```java
public void deleteConsumer(String groupId) throws ExecutionException, InterruptedException {
    ConfigurableApplicationContext configurableContext = (ConfigurableApplicationContext) applicationContext;
    KafkaMessageListenerContainer container = applicationContext.getBean(groupId, KafkaMessageListenerContainer.class);
    if (ObjectUtil.isNotNull(container)) {
        container.stop();
        // 从 ApplicationContext 中移除容器
        configurableContext.getBeanFactory().destroyBean(groupId);
        DefaultListableBeanFactory beanFactory = (DefaultListableBeanFactory) configurableContext.getBeanFactory();
        beanFactory.destroySingleton(groupId);
    }
}
```
