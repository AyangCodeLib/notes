IOC容器的加载过程分为两个阶段：**配置解析阶段**和**Bean创建阶段**
开始：new ApplicationContext()
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722912587806-fd2160b8-6dfa-4571-88a0-b54ca81ec987.png#averageHue=%23ded3cb&clientId=uecf3d6ba-7ba5-4&from=paste&height=492&id=ue5d6786d&originHeight=492&originWidth=986&originalType=binary&ratio=1&rotation=0&showTitle=false&size=307987&status=done&style=none&taskId=u5a83b71a-ab63-4bd0-bb70-c90284a7e64&title=&width=986)

1. 概念态：定义Bean信息（编写Bean的元数据）
2. 定义态：解析Bean的定义信息（元数据）生成BeanDefinition
3. 纯净态：通过反射创建Bean（没有进行依赖注入）
4. 成熟态：依赖注入等操作后生成单例Bean

# 配置解析阶段
概念态=>定义态

1. 读取配置：通过BeanDefinitionReader读取配置文件或配置类
2. invokeBeanFactoryPostProcessors：对外扩展，对内解耦。将概念态的bean注册为定义态
   1. 解析配置信息：如ComponentScan、Bean配置等
   2. 扫描类注解：根据ComonentScan扫描@Component、@Bean、@Configuration、@Import等注解... 
   3. 将符合的bean实例化一个BeanDefinition
   4. 将BeanDefinition对象缓存到BeanDefinitionMap当中
3. spring还会干许多其他的事情，比如国际化、加载环境变量、注册BeanPostProcessor、监听器等等
# Bean创建阶段
调用finishBeanFactoryInitialization方法来实例化单例的Bean
交给BeanFactory，调用getBean方法来生产Bean

1. 判断是否符合判断生产标准
   1. 是单例的
   2. 不是懒加载
   3. 不是抽象
2. 判断是否已经生成，去单例map中找，如果找到了直接返回，没有找到则创建

定义态=>纯净态

3. 实例化 Instantiation：推测初始化方法，通过反射的方法实例化对象 
4. spring合并处理BeanDefinition，判断是否需要属性注入

纯净态=>成熟态

5. 属性赋值 populate：DI的实现
6. 执行Aware：完成属性赋值之后，Spring会根据需要进行Aware回调，例如：BeanNameAware、BeanClassLoaderAware、BeanFactoryAware等
7. 初始化 initialization：创建AOP、生命周期的回调
8. 注册Bean：添加到单例池中
