# 下载linux版本的idea
链接地址： [IntelliJ IDEA: The Capable & Ergonomic Java IDE by JetBrains](https://www.jetbrains.com/idea/) 
# 解压到路径
在/usr/local路径下新建安装目录IDEA：
> sudo mkdir -p /usr/local/IDEA

解压下载的压缩包到指定目录，执行下面的命令
> sudo tar -zxvf ideaIU-2023.3.6.tar.gz -C /usr/local/IDEA

# 运行IDEA
```shell
cd /usr/local/IDEA/idea-IU-233.15026.9//bin/
./idea.sh
```
运行Intellij IDEA软件协议同意，然后点击Continue按钮 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1714914420929-709f627b-5d16-46ca-99d0-12433ca9a280.png#averageHue=%23dedede&clientId=u03a9d8ed-2a54-4&from=paste&height=493&id=uc69e6e30&originHeight=493&originWidth=646&originalType=binary&ratio=1&rotation=0&showTitle=false&size=55873&status=done&style=none&taskId=ufeae1737-ec03-405c-a4e0-7c3d9e8ee84&title=&width=646)
数据分享，如果不想分享，可以点击Don't Send 
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1714914453020-38c6665e-c59d-4152-8673-7323e149117c.png#averageHue=%23e3e1e1&clientId=u03a9d8ed-2a54-4&from=paste&height=504&id=u0c538e8b&originHeight=504&originWidth=653&originalType=binary&ratio=1&rotation=0&showTitle=false&size=55003&status=done&style=none&taskId=u58a71083-44e9-499a-8dae-3abe8b0970c&title=&width=653)
激活后安装成功
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1714914484780-b97638ee-3768-4432-88cf-bc1e4f3bf4a5.png#averageHue=%233a3f44&clientId=u03a9d8ed-2a54-4&from=paste&height=659&id=u9e50c16c&originHeight=659&originWidth=871&originalType=binary&ratio=1&rotation=0&showTitle=false&size=77188&status=done&style=none&taskId=u71385c43-4078-4310-aa4a-0011f239724&title=&width=871)
# 配置IDEA快捷方式
创建一个文件叫idea.desktop
> sudo vim /usr/share/applications/idea.desktop

启动vim后编写如下内容
```shell
[Desktop Entry]
Name=IntelliJ IDEA
Comment=IntelliJ IDEA
Exec=/usr/local/IDEA/idea-IU-233.15026.9/bin/idea.sh
Icon=/usr/local/IDEA/idea-IU-233.15026.9/bin/idea.png
Terminal=false
Type=Application
Categories=Developer;
```
# 查看是否生成图标
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1714914638468-4fa7f6b1-136f-4499-a8b0-90619c3c7559.png#averageHue=%23635c9d&clientId=u03a9d8ed-2a54-4&from=paste&height=217&id=u409788be&originHeight=217&originWidth=389&originalType=binary&ratio=1&rotation=0&showTitle=false&size=81247&status=done&style=none&taskId=u6a6e1252-fcf5-4519-9332-f9f16252b30&title=&width=389)
