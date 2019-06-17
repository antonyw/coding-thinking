# Spring 事务机制剖析
作为Java开发者，不免要接触到两种事务管理机制——全局事务和本地事务。

如果你不熟悉什么是全局事务或本地事务，让我们先看几个概念：
1. Resource Manager 简称RM，它负责存储并管理系统数据资源的状态，比如数据库服务器、JMS消息服务器都是RM。
2. Transaction Processing Monitor 简称TPM或TP monitor，它的职责是在分布式事务中协调包含多个RM的事务处理。TPM通常对应特定的中间件。
3. Transaction Manager 简称TM，它可以认为是TPM中的核心模块，直接负责多RM之间事务处理的协调工作，并且提供事务界定、事务上下文传播。
4. Application 以独立行使存在的或运行于容器中的应用程序，可以认为是事务边界的触发点。

理解了上面几个概念之后，其实全局事务和本地事务的区别就是在整个事务的过程中涉及到的RM数量多少。

### 全局事务
如果真个事务过程中涉及多个RM参与，那么就需要引入TPM来协调多个RM之间的事务处理。TPM通过一定的机制保证整个事务在多个RM之间的ACID属性。其实也就是我们常说的“分布式事务”。

### 局部事务
如果事务只涉及到一个RM参与，那么这就是局部事务，它并不需要TPM的介入。例如我们在订单生成后写一张表，或向一个消息队列发送一条消息。

### Java 中的事务
在Java原生环境中谈，对于局部事务来说，它对代码有很大的侵入性，而且对访问方式的抽象做的并不好。例如使用JDBC访问数据，那么就需要通过java.sql.Connection来处理事务；如果使用Hibernate访问数据，则需要通过Hibernate自己的类。这就容易导致事务管理代码与数据访问代码高度融合。

对于分布式事务来说，Spring之前使用全局事务的首选方法是通过EJB CMT（容器管理事务）。CMT是一种声明式事务管理（与程序化事务管理不同）。EJB CMT消除了与事务相关的JNDI查找的需要，尽管EJB本身的使用需要使用JNDI。它消除了编写Java代码以控制事务的大部分但不是全部的需要。但它有个限制，那就是必须借助EJB容器才能得以使用这种功能。

## Spring 事务管理突破
看过了Spring时代之前的局限性，接下来我们看一下**Spring为我们提供的一个重大革新——一致性编程模型（Consistent Programming Model）。**

它允许应用程序开发人员在任何环境中使用一致的编程模型。你只需编写一次代码，就可以从不同环境中的不同事务管理策略中受益。

与此同时，Spring 提供了声明式和编程式事务管理。

## Spring 事务抽象层
PlatformTransactionManager 是事务抽象架构的核心接口，既然是接口那么它就是为应用程序提供统一的界定方式。在接口内部很简洁的定义了三个函数。
```java
TransactionStatus getTransaction(TransactionDefinition def);
void commit(TransactionStatus status);
void rollback(TransactionStatus status);
```
该接口有一个默认的抽象实现类AbstractPlatformTransactionManager（**以下简称APTM**），打开这个抽象实现类就能看到它的子类是各个数据访问框架的支持类，其中包括jdbc、hibernate、jta等等。

**APTM 以模版方法的形式封装了固定的事务处理逻辑，而只将与事务资源相关的操作暴露给子类实现。** APTM 替子类实现了以下逻辑：
1. 判定是否存在当前事务，然后根据判断结果执行不同的处理逻辑；
2. 根据TransactionDefinition中定义的事务传播级别执行后续逻辑；
3. 根据情况挂起或者恢复事务；
4. 提交事务之前检查readonly字段是否被设置，如果是的话，以事务的回滚代替事务提交；
5. 在事务回滚的情况下，清理并恢复事务状态；
6. 如果事务的Synchonization处于active状态，在事务处理的规定时点触发注册的Synchonization回调接口。