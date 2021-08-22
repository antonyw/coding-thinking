# Kafka高可用机制（下）
上一篇文章中讲解了消息写入Broker的全流程，Leader副本会记录所有Follower的LEO，HW由各个副本自己记录。当Leader收到消息后，它会先更新自己记录的对应Follower的LEO，再将数据返回给Follower。本文将讲述在异常情况下Kafka如何实现高可用，以及Kafka在更新迭代中的优化。

根据前文的描述，Kafka使用HW+ISR机制保证Consumer能看到的消息，一定是写入成功的消息，从而保证了Broker到Consumer的数据一致性。以HW为切入点，我们看一个异常情况。

假设某一时间。Leader中有两条消息msg1和msg2，LEO和HW均为msg2。在Follower中，HW为msg1，LEO为msg2。根据前文的描述，F要同步到L的HW，需要携带自己的LEO发起FetchRequest请求，然后L计算F的LEO得到合适的HW值后，先记录在L本地。**注意，此时L已经更新了HW，但F还没有**，如下图所示。

![](../../img/Kafka-HA-error01.png)

