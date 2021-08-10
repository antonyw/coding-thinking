# Kafka 高可用机制
谈到高可用，一定绕不开的一个概念，那就是CAP原则。它指的是在一个分布式系统中， Consistency（一致性）、 Availability（可用性）、Partition tolerance（分区容错性），三者不可得兼。

![](../../img/Kafka-HA-01.png)

对于分布式系统的设计要求，我们的初衷是保证单个节点或几个节点失败的情况下，不影响系统整体提供服务，也就是要保证分区容错性。当出现这种情况时，我们通常有两种做法，不断重试直到恢复或者依赖存活的节点继续提供服务。

如果选择依赖存活节点提供服务，就是选择了AP，能保证可用性，但不能保证数据在各个节点的统一。反之选择的就是CP，重试直到恢复，保证所有节点数据相同。

## Kafka 高可用机制的实现
Kafka像许多分布式系统一样，依靠多副本机制实现高可用。但与其他分布式系统不同，Kafka的副本是属于分区的，一个分区内可能有一个或多个副本，其中一个是Leader副本，其余的为Follower副本。

![](../../img/kafka-system.png)

生产者发出的消息全部由Leader副本接收，Follower副本定时从Leader副本拉取消息。当Leader副本所在的Broker宕机时，所有Follower副本中**满足条件**的副本会被选为Leader继续提供服务。

可以看出Kafka的高可用机制要解决几个问题：

1. 满足什么条件的Follower才能晋升为Leader？
2. Leader和Follower之间如何进行同步？
3. 多副本的数据一致性以及可靠性如何？

## AR、ISR、LEO、HW
几个重要的概念，对于理解Kafka高可用机制至关重要。

AR：每个分区下所有副本的统称

ISR：所有与Leader副本保持一致的Follower副本集合，是AR的子集

LEO：标识每个分区中最后一条消息的下一个位置，分区的每个副本都有自己的LEO

HW：ISR中最小的LEO即为HW

## 满足什么条件的Follower才能晋升为Leader？
消息写入Leader，Follower拉取Leader，不难看出这过程中必定会有延时，有延时就会导致数据不一致。如果选出的Follower没有携带最新的消息，那么就会出现丢失消息的情况。**由此可见，我们应该选取出拥有最新进度的Follower作为Leader才不会丢失消息，也就是在ISR中的Follower。**

### 什么条件才能进入ISR集合？
从0.9版本开始Kafka使用Broker端配置参数replica.lag.time.max.ms来做抉择，当一个Follower之后Leader时间超过该参数值时，该Follower将会被踢出ISR集合。不难看出，仅以时间来衡量“ISR门槛”是有可能出现Follower永远无法跟上Leader。在Kafka源码注释中提及了两种情况：

1. Follower进程卡住，无法自主同步，比如发生频繁的Full GC
2. Leader写入流量过大，大量的I/O导致Follower始终落后于Leader

### ISR的伸缩
Kafka 在启动的时候会开启两个与 ISR 相关的定时任务，名称分别为“isr-expiration”和“isr-change-propagation”。isr-expiration会周期性的检查ISR集合是否需要缩小，而isr-change-propagation会周期性的检查ISR集合是否需要加入新的副本。

## Leader和Follower之间如何进行同步？
一条消息是如何写入Leader副本，又被Follower副本同步的，我们来看一下大体流程：

1. 生产者发送消息，消息被Leader副本接收
2. 消息写入Leader副本本地日志，并更新偏移量
3. Follower副本向Leader副本请求同步数据
4. Leader副本读取本地日志，并更新对应的Follower副本信息，再将结果返回给Follower副本
5. Follower副本收到消息后，追加到本地日志，并更新偏移量

某一时刻，生产者开始向Leader副本写入消息，此时Leader副本接收了5条消息后LEO=5

![](../../img/KAKFA-FLOW01.png)

Follower拉取到Leader中的日志消息，并且携带着Leader的HW=0，此时连个Follower各自更新本地日志。Follower1收到了3条消息，Follower2收到了4条消息。

![](../../img/KAFKA-FLOW02.png)

Follower再次拉取Leader消息，并携带自己的LEO，Leader取最小的LEO作为HW。此时HW=3才是被消费者可见的。

![](../../img/KAFKA-FLOW03.png)

Follower拉取到消息并携带Leader的HW=3，Follower各自更新本地日志和HW。

![](../../img/KAFKA-FLOW04.png)