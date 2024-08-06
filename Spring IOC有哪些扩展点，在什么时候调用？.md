1. 执行**BeanFactroyPostProcessor**的**postProcessBeanFactory**方法

- 作用：在注册BeanDefinition的可以对BeanFactory进行扩展
- 调用时机：IOC加载时注册BeanDefinition的时候调用

2. 执行**BeanDefinitionRegistryPostProcessor**的**postProcessBeanDefinitionRegistry**方法

- 作用：动态注册BeanDefinition
- 调用时机：IOC加载时注册BeanDefinition的时候会调用

