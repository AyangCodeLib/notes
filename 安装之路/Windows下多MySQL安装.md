
# 下载地址
[Mysql官网](https://dev.mysql.com/downloads/mysql/)
# 创建my.ini文件
```
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=D:\\Software\\mysql-5.7.44-winx64
# 设置mysql数据库的数据的存放目录
datadir=D:\\Software\\mysql-5.7.44-winx64\\data
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
max_connect_errors=10
# 服务端使用的字符集默认为UTF8
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
default_authentication_plugin=mysql_native_password
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8

# 忽略密码
#skip-grant-tables

```
```
[mysql]
 
# 设置mysql客户端默认字符集
default-character-set=utf8 
[mysqld]
#设置3307端口
port = 3307
# 设置mysql的安装目录
basedir=D:\\Software\\mysql-8.0.37-winx64
# 设置mysql数据库的数据的存放目录
datadir=D:\\Software\\mysql-8.0.37-winx64\\data
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES 
character-set-server = utf8mb4
performance_schema_max_table_instances = 600
table_definition_cache = 400
table_open_cache = 256
```
# 初始化
## MySQL5.7

1. mysqld -install MySQL57 --defaults-file="D:\Software\mysql-5.7.44-winx64\my.ini"
2. mysqld --initialize-insecure --user=mysql
- 目录下生成docs和data文件夹即为成功
3. 启动服务
- net start MySQL57
4. mysql -uroot -p 进入mysql管理页面
5. 修改密码
- update mysql.user set authentication_string='password(123456') where user='root' and host='localhost';
## MySQL80

1. mysqld --defaults-file=D:\Software\mysql-8.0.37-winx64\my.ini --initialize --console

执行完毕后，文件结构多了一个data目录。并记录初始化密码（后面第一次进入mysql修改密码需要）

2. mysqld -install MySQL80 --defaults-file="D:\Software\mysql-5.7.44-winx64\my.ini"
3. 启动服务
- net start MySQL80
4.     mysql -uroot -p生成的临时密码 --protocol=tcp --host=localhost --port=3307（选择端口）（进入管理页面）
5. 修改密码
-     ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'newpassword';
-     flush privileges;
