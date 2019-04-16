# Kafka Replication
在 Kafka 官方文档里把自己描述为“一个分布式的流处理平台”，既然是分布式系统就离不开一个老生常谈的话题——高可用（High Availability）。

在0.8版本之前的 Kafka 是没有实现 HA 机制的，一旦 Broker 宕机，则宕机期间该 Broker 上的所有 Partition 均无法再提供服务。如果这个机器永久性损坏，则这部分数据将永久丢失。这与 Kafka 的设计初衷相差甚远，所以在0.8版本之后的 Kafka 引入了复制功能提供 HA 机制。

## 复制功能如何实现 HA 
Kafka 的复制功能最小单元是分区（Partition），一个 Partition 可以拥有多个副本（replica），副本分为两种，一是首领副本（leader replica），二是跟随者副本（follower replica）。

- **leader replica**：每个 Partition 都有一个 leader replica。为了保证一致性，所有生产者和消费者的请求都会经过这个副本。
- **follower replica**：除 leader replica 之外的副本都是 follower replica。follower replica 不处理来自客户端的请求，它们唯一的人物就是从 leader 那里复制消息，保持与 leader 一致的状态。如果 leader 发生崩溃，其中一个 follower 会被提升为新 Leader。

