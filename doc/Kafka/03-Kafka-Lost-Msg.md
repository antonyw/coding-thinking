# Kafka 为什么会丢失消息
Kafka的设计初衷是 commit 的消息就能保证不丢失，在[这篇文章](./01-Kafka-Intro.md)我们说到不管 producer 还是 consumer 均支持 At Least Once 语义，也就是说消息绝不会丢失，但可能重传。但实际在生产环境中，还是有诸多情况会导致消息丢失，今天我们就来梳理一下消息到底是怎么丢的。

## 1. acks设置为0或1
在[这篇文章](./02-Kafka-Replication.md)中讲到 producer 有一个参数 acks 来配置当消息发送到 broker 时，需要多少 replica 确认收到消息。

- **acks = 0** 不用多说，但设置为0时，producer 不等待来自 broker 的确认继续发送下一批数据。此时如果网络出现丢包或 broker 出现宕机，producer 不会感知，因此在异常出现的时间段消息将会丢失。
- **acks=1** 这意味着 leader replica 收到消息即返回 producer 确认，那么当 leader replica 返回确认消息给 producer 后宕机，消息尚未来得及同步到 follower replica，那么消息就会丢失。除此之外，在 Linux 系统中消息会先写入缓存并没有直接写磁盘，在这里会有极低的可能导致消息丢失。

