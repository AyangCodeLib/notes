# 查询网站
[https://doc.knowstreaming.com/study-kafka/1-introduction](https://doc.knowstreaming.com/study-kafka/1-introduction)
# kafka segemnt
Kafka中存储和管理日志都是基于segment的。
## segment的作用

1. kafka的日志存储和消费，对外的最小力度是partion，也就是producer和consumer最小的选择粒度是某个topic的partition。
2. 每个partition有多个segment组成，这些segment一般是按照时间顺序产生的。
3. 在单个partition中只有一个处于active的segment，这个segment是正在写入的segment（假设为segmentA），当segmentA的大小达到一定的程度（或者是经过了一定的时长）就会产生一个新的segment（新的假设为segmentB），这个时候segmentA就不在有数据写入了，变成了不活跃的segment，而segmentB就是当前Active的segment。
4. 日志清理策略总是针对不活跃的segment进行的。
## segment生成相关的配置

1. segment.bytes：每个segment的大小，达到这个大小会产生新的segment，默认是1G
2. segment.ms：配置每隔n毫秒产生一个新的segment，默认是168h（7天）

这两个配置同时生效，那个条件先满足都会执行生产新的segment的动作。
# Kafka消息时间戳
在消息中增加了一个时间戳字段和时间戳类型。目前支持的时间戳类型有两种：CreateTime和LogAppendTime。

- CreateTime：表示producer创建这条消息的时间
- LogAppendTime：表示broker接收到这条消息的时间（严格来说是leader broker将这条消息写入到log的时间）
## 引入目的

1. 日志保存策略：Kafka目前会定期删除过期日志（log.retention.hours，默认是7天）。判断的依据就是比较Segment文件的最新修改时间。倘若最近一次修改发生于7天前，那么就会视该日志文件为过期日志，执行清除操作。但如果topic的某个分区曾经发生过分区副本的重分配，那么就有可能会在一个新的broker上创建Segment文件，并把该文件的最新修改时间设置为最新时间，这样设定的清除策略就无法执行了，尽管该Segment中的数据其实已经满足可以被删除的条件了。
2. 日志切片策略：与日志保存是一样的道理。当前Segment文件会根据规则对当前日志进行切分。即创建一个新的Segment文件，并设置其为active segment。其中有一条规则就是基于时间的（log.roll.hours，默认是7天），即当前Segment文件的最新一次修改发生于7天前的话，就强制创建一个新的Segment文件，并设置为active Segment。所以它也有同样的问题，即最近修改时间不是固定的，一旦发生分区副本重分配，该值就会发生变更，导致日志无法执行切分。（注意：log.retention.hours及其家族与log.rolling.hours及其家族不会冲突的，因为Kafka不会清除当前激活日志段文件）
3. 流式处理（Kafka streaming）：流式处理中需要用到消息的时间戳
## 消息格式的变化

1. 增加了timestap字段，表示时间戳
2. 增加了timestamp类型字段，保存在attribute属性地位的第四个比特上，0表示CreateTime；1表示LogAppendTime（低位前三个比特保存消息压缩类型）
## 客户端消息格式的变化

- ProducerRecord：增加了timestamp字段，允许producer指定消息的时间戳，如果不指定的话使用producer客户端的当前时间
- ConsumerRecord：增加了timestamp字段，允许消费消息时获取到消息的时间戳
- ProducerResponse：增加了timestamp字段，如果是CreateTime返回-1；如果是LogAppendTime返回写入该条消息时broker的本地时间
## 使用
Kafka broker config提供了一个参数：log.message.timestamp.type来统一指定集群中的所有topic使用哪种时间戳类型。用户也可以为单个topic设置不同的时间戳类型，具体做法是创建topic时覆盖掉全局配置
## Kafka内部时间戳处理流程
![image.png](https://cdn.nlark.com/yuque/0/2024/png/40608915/1714375390837-4012562a-36dd-471d-9f24-a2911fe5b44b.png#averageHue=%23fdfcfc&clientId=u9b186e86-0316-4&from=paste&height=522&id=ue0dbc884&originHeight=522&originWidth=643&originalType=binary&ratio=1&rotation=0&showTitle=false&size=91857&status=done&style=none&taskId=u3940dcbf-6f30-4730-8983-63c1be36cac&title=&width=643)
## 基于时间戳的功能

1. 根据时间戳来定位消息：之前的索引文件是根据offset消息的，从逻辑语义上并不方便使用，引入了时间戳之后，Kafka支持根据时间戳来查找定位消息
2. 基于时间戳的日志切分策略
3. 基于时间戳的日志清除策略
## 基于时间戳的消息定位
自0.10.0.1开始，Kafka为每个topic分区增加了新的索引文件：基于时间的索引文件：`<segment基础位移>.timeindex`，索引项间隔由`index.interval.bytes`确定。
具体的格式是时间戳+位移
时间戳记录的是该segment当前记录的最大时间戳
位移信息记录的是插入新的索引项时的消息位移信息
该索引文件中的每一行元组（时间戳T，位移offset）表示：该segment中币T晚的所有消息的位移都比offset大。
由于创建了额外的索引文件，所需的操作系统文件句柄平均要增加1/3（原本需要2个文件，现在需要3个）。
# Kafka日志清理策略
kakfa log的清理策略有两种：delete，compact，默认是delete
这个对应了kafka中每个topic对于record的管理模式

1. delete：一般是按照时间保留的策略，当不活跃的segment的时间戳是大于设置的时间的时候，当前segment就会被删除
2. compact：日志不会被删除，会被去重清理，这个模式要求每个record都必须有key，然后kafka会按照一定的时机清理segment中的key，对于同一个key只保留最新的那个key。同样的，compact也只针对不活跃的segment
> cleanup.policy:delete
> cleanup.policty:compact

## 日志清理delete策略
### delete相关配置
有一个topic（假设为user_topic）设置了`cleanup.policy:delete` 
那么user_topic使用的log删除策略就是delete，这个策略会周期性的检查partion中的不活跃的segment，根据配置采用两种方式删除一些旧的segment。
:::info

- retention.bytes:总的segement的大小限制，达到这个限制后会删除旧的segment，默认值为-1（不删除）
- retention.ms：segment的最后写入record的时间-当前时间>retention.ms的segment会被删除，默认是168h（7天）
:::
一些其他的辅助性配置
:::info

- log.retention.check.interval.ms：每隔多久检查一次是否有可以删除的log，默认是300s（5分钟）这个是broker级别的设置
- file.delete.delay.ms：在彻底删除文件前保留的时间，默认为1分钟。这个也是broker级别的设置
:::
在delete的日志策略下，如果想要日志保留3天
> retention.ms：259200000

但是有个场景问题：
日志产生的很慢，3天还没有达到1G，这个时候根据segment的产生策略，只有一个segment而且这个segment是活跃的，所以不能被删除，即使到了第四天可能依然不能被删除。这个时候如果我们依然想要优化存储，就可以使用领一个配置来使删除依然能够按时触发。
> segment.ms:43200000 # 12小时

这样的话每隔12小时，只要有新的数据进来都会产生一个新的segment，这样的话就可以出发3天的删除策略了。
所以比较重要的一点是：对于流量比较低的topic，要注意控制`segment.ms < retention.ms`
### 简单总结
kafka启用delete的清理策略的时候需要注意配置
```java
cleanup.policy: delete
segment.bytes: 每个segment的大小，达到这个大小会产生新的segment, 默认是1G
segment.ms: 配置每隔n ms产生一个新的segment,默认是168h,也就是7天
retention.bytes: 总的segment的大小限制，达到这个限制后会删除旧的segment,默认值为-1，就是不会删除
retention.ms: segment的最后写入record的时间-当前时间 > retention.ms 的segment会被删除，默认是168h, 7天
```
对于低流量的topic需要关注使用segment.ms 来配合日志的清理
## 日志清理compact策略
### 日志compact的使用场景
日志清理的compact策略，对于那种需要留存一份全量数据的需求比较有用，比如：
我用flink计算了所有用户的粉丝数，而且每5分钟更新一次，结果都存储到kafka当中。这个时候kafka相当于是一个数据总线，任何需要用户粉丝数的业务部门都可以从kafka中拿到这个数据。这个时候如果数据的保存使用delete策略，为了保存所有用户的粉丝数，只能设置不删除。
```java
retention.bytes: -1
retention.ms: Long.MAX #这个值需要自己去设置实际的数值值
```
这样的话，数据会无限膨胀，而且，很多数据是无意义的，因为业务方从kafka中消费数据的时候，实际上只是想知道用户的当前粉丝数是多少，不关注一个月前这个用户有多少粉丝数，但是这些数据都在kafka中存储，会造成无意义的消费。

kafka提供了一种叫做compact的清理策略，这个策略可以很好的帮助我们应对这种情况。
kafka的compact 策略要求每个record都要有key,kafka是根据key来进行去重合并的。每个key至少保留一个最新的值。
### compact的工作模式
对于每一个kafka partition的日志，以segment为单位，都会被分为两部分，已清理和未清理的部分。同时，未清理的那部分又分为可以清理的和不可清理的。对于可以清理的segment大致是下面的一个清理思路。
![](https://cdn.nlark.com/yuque/0/2024/png/40608915/1714372052092-2ced1769-c025-402d-8a86-ebdcddbe6125.png#averageHue=%23ececec&clientId=ued4e715d-5103-4&from=paste&id=u804eb80e&originHeight=399&originWidth=592&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u644b52dc-3059-4109-9688-a6276c716af&title=)
同时对于清理过后的segment如果太小，kafka也会有一定的策略去合并这些segemnt，防止segment碎片化。
```java
cleanup.policy: compact
```
配套的配置还有
:::info

- min.cleanable.dirty.ratio: 可以进行compact的脏数据的比例,dirtyRatio = dirtyBytes / (cleanBytes + dirtyBytes) 其中dirtyBytes表示可清理部分的日志大小，cleanBytes表示已清理部分的日志大小。这个配置也是为了提升清理的性价比设置的，因为清理数据需要对磁盘进行读写，开销并不小，如果你的数据只有很小的重复比例，实际上是没有清理的必要的。这个值默认是0.5 也就是脏了的数据达到了总数据的50%才会清理，一般情况下我如果开启了compact策略，都会将这个值设置为0.1，感觉这样对于想要消费当前topic的业务方更加友好。
- min.compaction.lag.ms: 这个设置了在一条消息在被produer发送到kafka当中之后，多久时间以内不会被compact，为了满足有些想要获取一定时间内的历史快照的业务。默认是0就是不会根据消息投递的时间来决定消息是否应该被compacted
:::
### tombstone消息
在compact下，还有一类比较特殊的消息，只有key，value值为null的消息，这一类消息如果合并了实际上也是没有意义的，因为没有值，所以kafka在compact的时候会删除value为null的消息，但是并不是在第一次去重的时候立刻删除，而是允许存储的更久一些。有一个特殊的配置来处理。

- delete.retention.ms: 这个配置就是专门针对tombstone类型的消息进行设置的。默认为24小时，也就是这个tombstone在当次compact完成后并不会被清理，在下次compact的时候，他的最后修改时间+delete.retention.ms>当前时间，才会被删掉。

注：这里面还有一点需要注意的是，如果你想测试tombstone的删除功能的话，请注意不要使用console-producer，他并不能产生value为null的record，坑死个人的，还是老老实实的用java-client跑一跑吧。
### 低流量topic的注意事项
同样，因为compact针对的是不活跃的segment，所以我们要对低流量的topic特别小心。
在流量较低的情况下。假如我们设置
```java
segment.bytes: 每个segment的大小，达到这个大小会产生新的segment, 默认是1G
segment.ms: 配置每隔n ms产生一个新的segment,默认是168h,也就是7天
```
这样的话，新的segment没有办法产生，也就无从进行compact了。
### 墓碑消息
当我们的清理策略cleanup.policy是compact的时候:
针对有 key 的 topic，对于每个 key，只存储最近一次的 value，既满足应用的需求，又加快了日志清理的速度。没有指定 key 的 topic 无法进行紧缩。
然后当你想把该 key 所有的消息都要清理掉的时候 , 你只需要发送一个墓碑消息, key 为你指定的 key，但是 value 为空。
那么紧缩线程就知道你想把该 Key 的所有消息都清理掉, 但是这个目标消息还是为你保留一段时间,以防你可能有一些业务上的处理。 这个保留的时间为 delete.retention.ms
### 简单总结
```java
cleanup.policy: compact
segment.bytes: 每个segment的大小，达到这个大小会产生新的segment, 默认是1G
segment.ms: 配置每隔n ms产生一个新的segment,默认是168h,也就是7天
retention.bytes: 总的segment的大小限制，达到这个限制后会删除旧的segment,默认值为-1，就是不会删除
retention.ms: segment的最后写入record的时间-当前时间 > retention.ms 的segment会被删除，默认是168h, 7天
min.cleanable.dirty.ratio: 脏数据可以容忍的比例，如果你的机器性能可以，而且数据量较大的话，建议这个值设置更小一些，对consumer更友好
min.compaction.lag.ms: 看业务有需要的话可以设置
```
对于低流量的topic需要关注使用segment.ms 来配合日志的清理

