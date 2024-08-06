# 下载地址
[https://www.oracle.com/java/technologies/downloads/#java8](https://www.oracle.com/java/technologies/downloads/#java8)
# 创建解压目录
```
sudo mkdir /opt/java
```
# 解压
```
sudo tar -zxvf jdk-8u411-linux-x64.tar.gz -C /opt/java
```
# 删除压缩包
```
sudo rm jdk-8u411-linux-x64.tar.gz
```
# 进入解压目录重命名文件
```
cd /opt/java
sudo mv jdk1.8.0_411/ jdk8
```
# 配置环境变量
```shell
// 修改用户环境变量
sudo gedit ~/.bashrc
//然后在文档后面添加下面配置
export JAVA_HOME=/opt/java/jdk8
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=#{JAVA_HOME}/bin:$PATH
```
# 使环境变量生效
```
source ~/.bashrc
```
# 设置默认jdk
```shell
sudo update-alternatives --install /usr/bin/java java /opt/java/jdk8/bin/java 300  
sudo update-alternatives --install /usr/bin/javac javac /opt/java/jdk8/bin/javac 300  
sudo update-alternatives --install /usr/bin/jar jar /opt/java/jdk8/bin/jar 300   
sudo update-alternatives --install /usr/bin/javah javah /opt/java/jdk8/bin/javah 300   
sudo update-alternatives --install /usr/bin/javap javap /opt/java/jdk8/bin/javap 300 
```
# 执行
```shell
sudo update-alternatives --config java
```
# 测试
```shell
java -version
javac -version
```
