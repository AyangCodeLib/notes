# Zookeeper概念
Zookeeper是一个分布式协调服务，可用于服务发现，分布式锁，分布式领导选举，配置管理等。
Zookeeper提供了一个类似于Linux文件系统的树形结构（可认为是轻量级的内存文件系统，但只适合存少量信息，完全不适合存储大量文件或者大文件），同时提供了对于每个节点的监控与通知机制。
# Zookeeper角色
Zookeeper集群是一个基于主从复制的高可用集群，每个服务器承担如下三种角色中的一种
## Leader

1. 一个Zookeeper集群同一时间只会有一个世纪工作的Leader，它会发起并维护与各Follower及Ovserver间的心跳。
2. **所有的写操作必须要通过Leader完成再由Leader将写操作广播给其他服务器。只要有超过半数节点（不包括observer节点）写入成功，该写请求就会被提高（类2PC协议）**
## Follower

1. 一个Zookeeper集群可能同时存在多个Follower，它会响应Leaader的心跳。
2. **Followe可直接处理并返回客户端的读请求，同时会将写请求转发给Leader处理**
3. **并且负责在Leader处理写请求时对请求进行投票。**
## Obsever
角色与Follower类似，但是无投票权。Zookeeper需保证高可用和强一致性，为了支持更多的客户端，需要增加更多server；**server增多，投票阶段延迟增大，影响性能；引入Observer，Observer不参与投票；Observers接受客户端的链接，并将写请求转发给Leader节点；**加入更多Observer节点，提高伸缩性，同时不影响吞吐量。
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1715311453346-cb9be462-9d5a-48ed-803d-bfeaec43499e.png#averageHue=%23d8d0bc&clientId=u48130278-fd40-4&from=paste&height=597&id=u560c4fb0&originHeight=597&originWidth=982&originalType=binary&ratio=1&rotation=0&showTitle=false&size=295453&status=done&style=none&taskId=u691186b3-84a2-446b-9297-52d114d9e1a&title=&width=982)
# ZAB协议
**事务编号Zxid（事务请求计数器+epoch）**
在ZAB（Zookeeper Atomic Broadcat， Zookeeper原子消息广播协议）协议的事务编号Zxid设计中，Zxid是一个64位的数字，其中低32位是一个简单的单调递增的计数器，**针对客户端每一个事务请求，计数器加1；**而高32位则代表Leader周期epoch的编号，**每个当选产生一个新的Leader服务器，就会从这个Leader服务器上取出其本地日志中最大事务的Zxid，并从中读取epoch值，然后加1，一次作为新的epoch，并将低32位从0开始计数。**
## Zxid
Zxid（Transaction id）类似于RDBMS中的事务ID，用于标识一次更新操作的Proposal（提议）ID。为了保证顺序性，该zkid必须单调递增。
## epoch
可以理解为当前集群所处的年代或者周期，每个Leader就像皇帝，都有自己的年号，所以每次改朝换代，Leader变更之后都会在前一个年代的基础上加1.这样就算**旧的Leader崩溃恢复之后，也没有人听他的了，因为Follower只听从当前年代的Leader的命令**。
## ZAB协议模式
ZAB协议有两种模式，分别是**恢复模式（选主）和广播模式（同步）**。当服务启动或者在领导者崩溃后，ZAB就进入恢复模式，当领导者被选举出来且大多数Server完成了和Leader的状态同步以后，恢复模式就结束了。状态同步保证了Leader和Server具有相同的系统状态。
## ZAB协议4阶段
### Leader election（选举阶段-选出准Leader）
节点在一开始都处于选举阶段，只要有一个节点得到超半数节点的票数，他就可以当选准Leader。只有到达广播阶段（broadcast）准Leader才会成为真正的Leader。这一阶段的目的就是为了选出一个**准Leader**，然后进入下一个阶段。
### Discovery（发现阶段-接受提议、生成epoch、接受epoch）
在这个阶段，**Followers跟准Leader进行通信，同步Followers最近接受的事务提议。**这个阶段的**主要目的是发现当前大多数节点接受的最新提议，**并且**准Leader生成新的epoch，让Followers接受，更新他们的accepted epoch。**
一个Follower只会连接一个Leader，**如果有一个节点f认为另一个Follower p是Leader，f在尝试连接p时会被拒绝，f被拒绝之后，就会进入重新选举阶段。**
### Synchronization（同步阶段-同步Follower副本）
同步阶段主要是利用**Leader前一阶段获得的最新提议历史，同步集群中所有的副本。只有当大多数节点都同步完成，准Leader才会成为真正的Leader。**Follower只会接受zxid比自己大的lastZxid的提议。
### Broadcast（广播阶段-Leader消息广播）
到了这个阶段，**Zookeeper集群才能正式对外提供事务服务，并且Leader可以进行消息广播。**同时如果有新的节点加入，还需要对新节点进行同步

ZAB提交事务并不像2PC一样需要全部Follower都ACK，**只需要得到超过半数的节点的ACK就可以了。**
## ZAB协议JAVA实现（FLE-发现阶段和同步合并为Recovery Phase（恢复阶段））
协议的JAVA版本实现跟上面的定义有些不同，选举阶段使用的是Fast Leader Election（FLE），它包含了选举的发现职责。因为FLE会选举拥有最新提议历史的节点作为leader，这样就省去了发现最新提议的步骤。实际的实现将发现阶段和同步阶段合并为Recovery Phase（恢复阶段）。所以ZAB的实现只有三个阶段：Fast Leader Election；Recovery Phase；Broadcast Phase。
## 投票机制
### 流程
**每个server首先给自己投票，然后用自己的选票和其他server选票丢比，权重大的胜出，使用权重较大的更新自身选票箱。**具体选举过程如下：

1. 每个Server启动以后**都询问其他的Server它要投票给谁。**对于其他server的询问，server每次根据自己的状态都回复自己推荐的leader的id和上一次处理事务的zxid（系统启动时每个server都会推荐自己）
2. 收到所有server回复以后，就**计算出zxid最大的那个server**，并将这个server相关信息设置成下一次要投票的server。
3. 计算这过程中**获得票数最多的server为获胜者**，如果获胜者的票数超过半数，则该server被选为leader。否则继续这个过程，知道leader被选举出来。
4. leader就会开始等待server连接
5. Follower连接leader，将最大的zxid发送给leader
6. leader根据Follower的zxid确定同步点，至此选举阶段完成。
7. 选举阶段完成leader同步后通知Follower已经成为uptodate状态。
8. Follower收到uptodate消息后，又可以重新接受client的重新进行服务了
### Sample
目前有5台服务器，每台服务器均没有数据，它们的编号分别是1,2,3,4,5按编号依次启动，它们的选举过程如下：

1. 服务器1启动，给自己投票，然后发投票信息，由于其他机器还没有启动所以它收不到反馈信息，服务器1的状态一直属于Looking。
2. 服务器2启动，给自己投票，同时与之前启动的服务器1交换结果，由于服务器2的编号大所以服务器2胜出，但此时投票数没有大于半数，所以两个服务器的状态依然是LOOKING。
3. 服务器3启动，给自己投票，同时与之前启动的服务器1,2交换信息，由于服务器3的编号最大所以服务器3胜出，此时投票数正好大于半数，所以服务器3成为领导者，服务器1,2成为小弟。
4. 服务器4启动，给自己投票，同时与之前启动的服务器1,2,3交换信息，尽管服务器4的编号大，但之前服务器3已经胜出，所以服务器4只能成为小弟。
5. 服务器5启动，后面的逻辑同服务器4成小弟。
# Zookeeper工作原理（原子广播）

1. **Zookeeper的核心是原子广播**，这个机制保证了各个server之间的同步，实现这个机制的协议叫做zab协议。zab协议有两种模式，分别是恢复模式和广播模式。
2. 当服务启动或者在领导者崩溃之后，zab就进入了恢复模式，当领导者被选举出来，且大多数server的完成了和leader的状态同步以后，恢复模式就结束了。
3. 状态同步保证了leader和server具有相同的系统状态
4. **一旦leader已经和多数的Follower进行了状态同步后，他就可以开始广播消息了**，即进入广播状态。这时候当一个server加入Zookeeper服务中，它就会在恢复模式下启动，发现leader并和leader进行状态同步。待到同步结束，它也参与消息广播。Zookeeper服务一直维持在Broadcast状态，直到leader崩溃了或者leader失去了大部分的Followers支持。
5. 广播模式需要保证proposal呗按顺序处理，因此zk采用了递增的事务id号（zxid）来保证所有提议（proposal）都在被踢出的时候加上了zxid。
6. 实现中zxid是一个64位的数字，它高32位是epoch用来表示leader关系是否改变，每次一个leader被选出来，它都会有一个新的epoch。低32位是个递增技术。
7. 当leader崩溃或者leader失去大多数的Follower，这时候zk进入恢复模式，恢复模式需要重新选举出一个新的leader，让所有的server都恢复到一个正确的状态。
# Znode有四种形式的目录形式

1. PERSISTENT:持久的节点
2. EPHEMEPAL:暂时的节点
3. PERSISTENT_SEQUENTIAL:持久化顺序编号目录节点
4. EPHEMERAL_SEQUENTIAL:暂时化顺序编号目录节点

