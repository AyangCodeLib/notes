- BeanFactory是Spring中**非常核心的一个顶层接口**；
- 它是Bean的工厂，它的**主要职责就是生产Bean**;
- 它实现了**简单工厂的设计模式**，通过调用getBean传入表示生产一个Bean；
- 它有非常多的实现类，每个工厂都有不同的职责（单一职责）功能，最强大的工厂是：**DefaultListableBeanFactory** Spring底层就是使用的该实现工厂进行生产Bean的

进行生产Bean的

- BeanFactory它也是容器   Spring容器（管理着某个对象的生命周期）=>Bean容器

![image.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722506106700-d3c40ad8-5fab-4614-a617-e562fba54bde.png#averageHue=%232e2e2e&clientId=u1751a1ab-f89d-4&from=paste&height=667&id=u6d8b3c11&originHeight=667&originWidth=1535&originalType=binary&ratio=1&rotation=0&showTitle=false&size=115810&status=done&style=none&taskId=ua82873e8-7eca-4df3-bbb6-52d4e1493e8&title=&width=1535)
