### 下载docker
根据提示输入`y`。
```bash
#yum install docker（这个下载的是老版本，不要用视频的这个，用下面的）
# 1.阿里云镜像资源（先执行这个下载加速）
yum-config-manager --add-rep https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
#2.安装docker
yum install -y docker-ce
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/26318626/1683704436822-54cb9067-4c6f-4ae5-805d-078619e8691c.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_15%2Ctext_TWFsbENoYXQ%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10#averageHue=%230b0705&clientId=u460d63d1-f319-4&from=paste&height=235&id=ubcfb21d0&originHeight=235&originWidth=509&originalType=binary&ratio=1&rotation=0&showTitle=false&size=12069&status=done&style=none&taskId=u780fc43c-74a4-4127-8b9f-fb586c79a7e&title=&width=509)
### **启动Docker服务**
安装完成后，使用下面的命令来启动 docker 服务，并将其设置为开机启动：
```bash
service docker start
chkconfig docker on
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/26318626/1683704471701-46c08be5-b7dd-44d0-9fa9-0235785fb7a3.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_22%2Ctext_TWFsbENoYXQ%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10#averageHue=%23090604&clientId=u460d63d1-f319-4&from=paste&height=117&id=uac0ec15e&originHeight=117&originWidth=757&originalType=binary&ratio=1&rotation=0&showTitle=false&size=11799&status=done&style=none&taskId=u14f94d26-cd92-4d15-bca4-d760a929acb&title=&width=757)
测试Docker是否安装成功
```bash
docker version
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/26318626/1696767474724-4eab4de3-e304-4b6d-a49d-53d88e938a3e.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_16%2Ctext_TWFsbENoYXQ%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10#averageHue=%23161310&clientId=u4a887ebf-68be-4&from=paste&height=47&id=u65c06338&originHeight=59&originWidth=545&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=6778&status=done&style=none&taskId=u7bd4a93e-7c40-4e63-b4eb-0b1fd0a40ef&title=&width=436)
输入上述命令，返回docker的版本相关信息，证明docker安装成功。
### 设置国内镜像
```bash
vi  /etc/docker/daemon.json

#添加后
{
    "registry-mirrors": ["https://mirror.ccs.tencentyun.com"],
    "live-restore": true
}
```
•依次执行以下命令，重新启动 Docker 服务。
```bash
systemctl daemon-reload
service docker restart
```
• 检查是否生效
```bash
docker info
```
查看是否有如下信息
```bash
Registry Mirrors:
    https://mirror.ccs.tencentyun.com/
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/26318626/1683704567658-8b5e8aa2-a441-4cb1-a1d9-31cdefffddeb.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_10%2Ctext_TWFsbENoYXQ%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10#averageHue=%23131008&clientId=u460d63d1-f319-4&from=paste&height=310&id=ua46eb0a8&originHeight=310&originWidth=355&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18420&status=done&style=none&taskId=ud2f6a493-d733-4418-ad08-96ace3bd299&title=&width=355)
### Docker Compose的安装
我们一般都是通过docker compose来安装中间件，所以这个必不可少。会比较慢，可手动下载
```bash
curl -L https://github.com/docker/compose/releases/download/1.28.6/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/26318626/1683705236333-16b06e15-d88d-416d-8b99-9249a95bbd8f.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_18%2Ctext_TWFsbENoYXQ%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10#averageHue=%230e0a07&clientId=u460d63d1-f319-4&from=paste&height=86&id=u11062fd6&originHeight=86&originWidth=635&originalType=binary&ratio=1&rotation=0&showTitle=false&size=7306&status=done&style=none&taskId=u2e768f96-e949-4742-b7bd-e4fd6dec1fd&title=&width=635)
[下载地址](https://github.com/docker/compose/releases/download/1.28.6/docker-compose-Linux-x86_64)，下载后执行命令 
[百度云下载地址](https://pan.baidu.com/s/1xbK9p9Gz_2qVNgZhU2HseA?pwd=8888) 
```bash
cd /usr/local/bin
rz #直接通过rz导入文件
```
将可执行权限应用于二进制文件：
```bash
sudo chmod +x /usr/local/bin/docker-compose
```
测试是否安装成功：
```bash
docker-compose --version
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/26318626/1683705256911-b524b725-2594-495c-af5b-69b17a57f9fd.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_11%2Ctext_TWFsbENoYXQ%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10#averageHue=%2315100b&clientId=u460d63d1-f319-4&from=paste&height=44&id=u98a6b8ed&originHeight=44&originWidth=390&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3571&status=done&style=none&taskId=u6751bec8-9865-4bbe-8cfa-2fbdee9b3bc&title=&width=390)
### **常用命令**
除过以上我们使用的Docker命令外，Docker还有一些其它常用的命令
拉取docker镜像
`docker pull image_name`
查看宿主机上的镜像，Docker镜像保存在/var/lib/docker目录下:
`docker images`
删除镜像
`docker rmi docker.io/tomcat:7.0.77-jre7 或者 docker rmi b39c68b7af30`
查看当前有哪些容器正在运行
`docker ps`
查看所有容器
`docker ps -a`
启动、停止、重启容器命令：
`docker start container_name/container_id docker stop container_name/container_id docker restart container_name/container_id`
后台启动一个容器后，如果想进入到这个容器，可以使用attach命令：
`docker attach container_name/container_id`
删除容器的命令：
`docker rm container_name/container_id`
删除所有停止的容器：
`docker rm $(docker ps -a -q)`
查看当前系统Docker信息
`docker info`
从Docker hub上下载某个镜像:
`docker pull centos:latest docker pull centos:latest`
查找Docker Hub上的nginx镜像
`docker search nginx`
执行docker pull centos会将Centos这个仓库下面的所有镜像下载到本地repository。
