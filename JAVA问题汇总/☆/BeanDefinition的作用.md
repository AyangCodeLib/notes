主要负责存储Bean的**定义信息**，决定Bean的创建方式
例如：

- 类的名称或是类的类型
- 类的全限定名
- 类的定义域
- 类是否懒加载
- 是否抽象
- 属性信息
- 依赖的类
- 等等

后续BeanFactory根据这些信息就可以生产Bean：比如实例化 可以通过class进行反射进而得到实例对象，比如lazy，则不会在ioc加载时创建Bean
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722907735101-3fb255c2-c484-4f83-b2aa-ae6fc4419916.png#averageHue=%23f7f7f7&clientId=uceb0572e-0d77-4&from=paste&height=400&id=u5ee90d15&originHeight=400&originWidth=1214&originalType=binary&ratio=1&rotation=0&showTitle=false&size=163397&status=done&style=none&taskId=ubabdce36-afb8-4b7d-b125-1254b2ed3c9&title=&width=1214)
