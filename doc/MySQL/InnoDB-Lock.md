# InnoDB 引擎中的锁
最近在面试候选人聊到MySQL的InnoDB引擎和MyISAM引擎的区别时，几乎所有人都会说到一点：InnoDB能够支持行级锁，MyISAM只支持表级锁，所以InnoDB效率高一些。

这句话本身并没有问题，通常我会再问一句“InnoDB在任何情况下都能通过行级锁解决数据可能不一致的问题吗？”，可以详细讲下去的候选人似乎并没有多少。感觉更多的人可能是去搜索引擎直接搜“InnoDB和MyISAM的区别”并默记下来，所以才知道这个特性。

这篇文章的目的是想彻底梳理InnoDB引擎中使用的锁，以及不同情况下的不同锁算法。
