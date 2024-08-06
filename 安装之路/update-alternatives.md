# 注册
以jdk为例，安装了jdk以后，先要在update-alternatives工具中注册；
```shell
update-alternatives --install /usr/bin/java java /opt/jdk1.8.0_91/bin/java 200
update-alternatives --install /usr/bin/java java /opt/jdk1.8.0_111/bin/java 300
```
其中:
第一个参数--install表示向update-alternatives注册服务名。
第二个参数是注册最终地址，成功后将会把命令在这个固定的目的地址做真实命令的软链，以后管理就是管理这个软链；
第三个参数：服务名，以后管理时以它为关联依据。
第四个参数，被管理的命令绝对路径。
第五个参数，优先级，数字越大优先级越高。
# 查看已注册列表
```shell
# update-alternatives --display java
java - status is auto.
 link currently points to /opt/install/jdk1.8.0_111/bin/java
/opt/install/jdk1.8.0_91/bin/java - priority 200
/opt/install/jdk1.8.0_111/bin/java - priority 300
Current `best' version is /opt/install/jdk1.8.0_111/bin/java.
```
# 修改命令版本
注意--display开关使用时第一行信息：
```
java - auto/manual mode
```
默认为自动版本，根据优先级，使用优先级高的。
下面手动修改为jdk1.8.0_91：
## 交互式修改
交互式会提示一所有可用的列表， 选择对应的索引确认。
```shell
# update-alternatives --config java

There are 2 programs which provide 'java'.

  Selection    Command
-----------------------------------------------
   1           /opt/install/jdk1.8.0_91/bin/java
*+ 2           /opt/install/jdk1.8.0_111/bin/java

Enter to keep the current selection[+], or type selection number: 1

# java -version
java version "1.8.0_91"
Java(TM) SE Runtime Environment (build 1.8.0_91-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.91-b14, mixed mode)

# update-alternatives --display java
java - status is manual.
 link currently points to /opt/install/jdk1.8.0_91/bin/java
/opt/install/jdk1.8.0_91/bin/java - priority 200
/opt/install/jdk1.8.0_111/bin/java - priority 300
Current `best' version is /opt/install/jdk1.8.0_111/bin/java.
```
看到当前状态变成了manual。
修改为自动：
```shell
# update-alternatives --auto java
# java -version
java version "1.8.0_111"
Java(TM) SE Runtime Environment (build 1.8.0_111-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.111-b14, mixed mode)

# update-alternatives --display java
java - status is auto.
 link currently points to /opt/install/jdk1.8.0_111/bin/java
/opt/install/jdk1.8.0_91/bin/java - priority 200
/opt/install/jdk1.8.0_111/bin/java - priority 300
Current `best' version is /opt/install/jdk1.8.0_111/bin/java.
```
又改为按照优先级高的了。
## 立即修改
除了交互式修改，也可以使用一条命令直接修改，修改后立即生效。
```shell
# update-alternatives --set java /opt/jdk1.8.0_91/bin/java
```
该情形适用于你对路径很熟悉，或者你已经进入了该路径：
```shell
# cd /opt/jdk1.8.0_91/bin/
# update-alternatives --set java $PWD/java
```
# 管理软件包
开始我们以java为例，作为jre运行环境可以，但如果你作为开发测试环境，你会发现javac找不到。
```
# javac
-bash: javac: command not found
```
原因是我们只对java命令做了版本管理。
事实上，update-alternatives的原理是软链管理，可以处理目录。那么我们就可以把整个软件包目录都纳入管理。
首先清理掉原来配置的java命令配置。
```shell
# update-alternatives --remove java /opt/jdk1.8.0_91/bin/java
# update-alternatives --remove java /opt/jdk1.8.0_111/bin/java
```
ubuntu里可以直接使用下面的全部清除，centos没有：
```
# update-alternatives --remove-all java
```
注册javahome管理
```shell
# update-alternatives --install /usr/local/jdk jdk /opt/jdk1.8.0_111 300
update-alternatives: using /opt/jdk1.8.0_111 to provide /usr/local/jdk (jdk) in auto mode

# update-alternatives --install /usr/local/jdk jdk /opt/jdk1.8.0_91 200

# update-alternatives --display jdk
jdk - auto mode
  link best version is /opt/jdk1.8.0_111
  link currently points to /opt/jdk1.8.0_111
  link jdk is /usr/local/jdk
/opt/jdk1.8.0_111 - priority 300
/opt/jdk1.8.0_91 - priority 200
```
配置jdk环境变量，指向注册的软链地址。
```shell
# echo export JAVA_HOME=/usr/local/jdk >>~/.bash_profile
# echo export PATH=$PATH:$JAVA_HOME/bin >>~/.bash_profile
# source ~/.bash_profile
```
管理JAVA_HOME
```shell
# echo $JAVA_HOME
/usr/local/jdk

# java -version
java version "1.8.0_111"
Java(TM) SE Runtime Environment (build 1.8.0_111-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.111-b14, mixed mode)

# javac -version
javac 1.8.0_111

# update-alternatives --config jdk
There are 2 choices for the alternative jdk (providing /usr/local/jdk).

  Selection    Path               Priority   Status
------------------------------------------------------------
* 0            /opt/jdk1.8.0_111   300       auto mode
  1            /opt/jdk1.8.0_111   300       manual mode
  2            /opt/jdk1.8.0_91    200       manual mode

Press <enter> to keep the current choice[*], or type selection number: 2
update-alternatives: using /opt/jdk1.8.0_91 to provide /usr/local/jdk (jdk) in manual mode

# echo $JAVA_HOME
/usr/local/jdk

# java -version
java version "1.8.0_91"
Java(TM) SE Runtime Environment (build 1.8.0_91-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.91-b14, mixed mode)

# javac -version
javac 1.8.0_91
```
