BeanFactory主要职责用于生产Bean，是Spring容器，管理着Bean的生命周期
applicationContext通常被较为Spring上下文，也是个Spring容器，也管理着Bean的生命周期
共同点：都可以做为容器，管理Bean的生命周期
关系：ApplicationContext实现了FactoryBean
区别：

1. BeanFactory getBean 用于生产Bean
   1. 内存占用率小，可以用于嵌入式设备
2. ApplicationContext实现了BeanFacotry
3. ApplicationContext不生产Bean，而是通知BeanFactory来进行生产，getBean是一个门面方法
4. ApplicationContext扩展了BeanFactory功能，做的事情比BeanFactory更多：
   1. 自动帮我们把配置的bean，注册到容器中
   2. 加载环境变量
   3. 支持多语言
   4. 实现事件监听
   5. 注册很多的对外扩展点


