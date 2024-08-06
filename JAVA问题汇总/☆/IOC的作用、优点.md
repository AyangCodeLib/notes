IOC就是控制反转
UserService service = new UserService(); 

- 要修改UserService类的时候需要一个个修改。将UserService修改为单例对象时，需要挨个将用到的地方进行删除
- 耦合度太高
- 维护不方便

引入IOC 就将创建对象的控制权交给SpringIOC，以前由程序员自己控制对象创建，现在交给Spring的ioc去创建，如果要去使用需要通过DI（依赖注入）@Autowired自动注入 就可以使用对象了。
优点：

- 集中管理对象，便于维护
- 解耦，降低耦合度
