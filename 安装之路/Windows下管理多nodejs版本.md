# 安装nvm
下载并安装nvm，下载地址：[https://github.com/coreybutler/nvm-windows/releases](https://github.com/coreybutler/nvm-windows/releases)，下载.zip后缀的这个文件，下载后解压安装即可
![QQ_1722067747776.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722067753403-c7582886-0ef2-489a-8476-b6b7a5fb7f99.png#averageHue=%23fefefd&clientId=u303c3575-1893-4&from=paste&height=492&id=ued15f9c8&originHeight=492&originWidth=1025&originalType=binary&ratio=1&rotation=0&showTitle=false&size=66365&status=done&style=none&taskId=u222aa3a2-b832-415d-8503-649baf3162f&title=&width=1025)
# 配置镜像（建议用nrm管理）
[**nrm安装**](#rqqby)
配置镜像源，从安装目录中找到settings.txt文件将一下配置复制进去
![1722068059222.jpg](https://cdn.nlark.com/yuque/0/2024/jpeg/40608915/1722068060198-a94c2ab5-7941-4419-89bb-55eb74d295c9.jpeg#averageHue=%23f8f7f7&clientId=u303c3575-1893-4&from=paste&height=524&id=u00550dfa&originHeight=524&originWidth=1242&originalType=binary&ratio=1&rotation=0&showTitle=false&size=54241&status=done&style=none&taskId=u7148dbb0-77c6-438a-8c72-3eba0343977&title=&width=1242)
```
node_mirror: http://npmmirror.com/mirrors/node/
npm_mirror: http://registry.npmmirror.com/mirrors/npm/
```
# 查看安装的版本
查看安装的版本号，检查安装是否成功：nvm
![QQ_1722068124733.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722068126586-901ebb41-ffde-4e7d-b073-f4cd1ea51faf.png#averageHue=%23191919&clientId=u303c3575-1893-4&from=paste&height=628&id=u39d4881b&originHeight=628&originWidth=1115&originalType=binary&ratio=1&rotation=0&showTitle=false&size=82612&status=done&style=none&taskId=u94786160-0193-4921-993c-3ed4993f7e8&title=&width=1115)
# 查看nodejs所有版本
```
nvm list available
```
![QQ_1722068236503.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722068238001-262c3642-73d3-4f5d-9012-42aec4219df2.png#averageHue=%23151515&clientId=u303c3575-1893-4&from=paste&height=628&id=u4f004149&originHeight=628&originWidth=1115&originalType=binary&ratio=1&rotation=0&showTitle=false&size=65797&status=done&style=none&taskId=u9011fd5a-1ae5-4414-adf1-7b7d9391dd6&title=&width=1115)
# 安装指定版本
Node.js 用 18.x 的稳定版、NPM 用 9.x
```
nvm install 18.20.4
```
![QQ_1722068306779.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722068306790-40583ae9-f887-4208-903b-c696c382a8a3.png#averageHue=%23151515&clientId=u303c3575-1893-4&from=paste&height=212&id=ua99b4c2e&originHeight=212&originWidth=597&originalType=binary&ratio=1&rotation=0&showTitle=false&size=16231&status=done&style=none&taskId=u5e6e6bb2-94b8-4a80-8d13-0764dd3d33d&title=&width=597)
# 使用指定版本或切换到指定版本
```
nvm use 18.20.4
```
![QQ_1722068354411.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722068354063-1fedba6e-a2e4-48b1-8d24-d79b7e88c78a.png#averageHue=%231c1c1c&clientId=u303c3575-1893-4&from=paste&height=71&id=u13d5f1cc&originHeight=71&originWidth=312&originalType=binary&ratio=1&rotation=0&showTitle=false&size=4107&status=done&style=none&taskId=u1a3b209b-ce6e-477a-bdbf-66c28b56726&title=&width=312)
# 查看当前版本信息
```
node -v （查看当前版本）
nvm list （查看已安装的所有nodejs版本）
```
![QQ_1722068390605.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722068390028-e566fff2-dfd7-4fcb-bda6-a6a9a68cfe0b.png#averageHue=%23131313&clientId=u303c3575-1893-4&from=paste&height=151&id=u1384af81&originHeight=151&originWidth=504&originalType=binary&ratio=1&rotation=0&showTitle=false&size=9273&status=done&style=none&taskId=u4c1618a6-26c0-4f0d-acc5-10835dd86b7&title=&width=504)
# 卸载指定版本
```
nvm uninstall 18.20.4
```
# 其他nvm常用命令
```
nvm off                     // 禁用node.js版本管理(不卸载任何东西)
nvm on                      // 启用node.js版本管理
nvm install <version>       // 安装node.js的命名 version是版本号 例如：nvm install 8.12.0
nvm uninstall <version>     // 卸载node.js是的命令，卸载指定版本的nodejs，当安装失败时卸载使用
nvm ls                      // 显示所有安装的node.js版本
nvm list available          // 显示可以安装的所有node.js的版本
nvm use <version>           // 切换到使用指定的nodejs版本
nvm v                       // 显示nvm版本
nvm install stable          // 安装最新稳定版
```
# 设置存储地址
## 方法一（证书可能过期了，建议方法二）：
```java
npm config set registry https://registry.npm.taobao.org
```
## 方法二：
```java
npm install nrm -g --save
nrm ls
nrm use ***
```
# 设置缓存地址
在已经安装成功 [node](https://so.csdn.net/so/search?q=node&spm=1001.2101.3001.7020) 后，修改 npm 缓存目录，首先在 node 的安装目录下创建两个文件夹 node_global 和 node_cache
![QQ_1722074539517.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1722074553188-52ca15e1-c0ca-4c09-b2ca-46c5a5172c87.png#averageHue=%23f9f9f8&clientId=u936b99f3-dd9b-4&from=paste&height=630&id=u5d3f50a0&originHeight=630&originWidth=1125&originalType=binary&ratio=1&rotation=0&showTitle=false&size=82947&status=done&style=none&taskId=u65d6eef6-7cf4-404a-8cf3-09c98d17fd1&title=&width=1125)
设置 npm 全局包下载路径
```java
npm config set prefix "D:\Software\node\nodejs\node_global"
```
设置 npm 缓存路径
```java
npm config set cache "D:\Software\node\nodejs\node_cache"
```
