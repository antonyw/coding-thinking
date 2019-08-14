# Kafka 为什么会丢失消息
Kafka的设计初衷是 commit 的消息就能保证不丢失，在[这篇文章](./01-Kafka-Intro.md)我们说到不管 producer 还是 consumer 均支持 At Least Once 语义，也就是说消息绝不会丢失，但可能重传。但实际在生产环境中，还是有诸多情况会导致消息丢失，今天我们就来梳理一下消息到底是怎么丢的。

## 1. acks设置为0或1
在[这篇文章](./02-Kafka-Replication.md)中讲到 producer 有一个参数 acks 来配置当消息发送到 broker 时，需要多少 replica 确认收到消息。

