便捷记忆：配置元数据->Bean定义->Bean工厂->反射->依赖注入

1. Bean工厂：Spring的Bean工厂负责创建和管理Bean对象。Bean工厂可以是BeanFactory接口的实现，如DefaultListableBeanFactory。Bean工厂负责解析配置元数据，根据Bean定义创建Bean对象，并将其放入容器中进行管理。
```java
public static BaseService getBean(String className){
    BaseService baseService = null;
    if("user".equals(beanName)){
        baseService=new UserServiceImpl();
    }
    else if("role".equals(beanName)){
        baseService=new RoleServiceImpl();
    }
    ....
    return baseService;
}
```

2. 反射：Spring使用java的反射机制来实现动态的创建和管理Bean对象。通过反射，Spring可以在运行时动态地实例化Bean对象、调用Bean的方法和设置属性值
```java
public static BaseService getBean(String className){
    BaseService baseService = null;
    try{
        baseService = (BaseService) Class.forName(className).newInstance();
    }catch(Exception e){
        e.printStackTrace();
    }
}
```

3. 配置元数据：Spring使用配置元数据来描述Bean的定义和依赖关系。配置元数据可以通过XML配置文件、注解和Java代码等方式进行定义。Spring在启动时会解析配置元数据，根据配置信息创建和管理Bean对象。
4. BeanDefinition：Spring使用BeanDefinnition来表述Bean的属性、依赖关系和生命周期等信息。BeanDefinition可以通过XML配置文件中的<bean>元素、注解和Java代码中的@Bean注解等方式进行定义。BeanDefinition包含了Bean的类名、作用域、构造参数、属性值、是否延迟初始化、所依赖的类、销毁调用的方法名等信息
5. 依赖注入：Spring使用依赖注入来解决Bean之间的依赖关系。通过依赖注入，Spring容器负责将Bean所依赖的其他Bean注入到它们之中。Spring使用反射和配置元数据来确定依赖关系，并在运行时注入。

