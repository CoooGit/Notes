# Kafka笔记

## 1 Kafka的概念

Kafka是一个实时数据处理系统，支持横向扩展且具备高可靠性。简单地讲，Kafka是一个分布式发布-订阅消息系统，是消息中间件的一种。Kafka提供的能力有：

- 发布-订阅记录流；
- 以容错的方式存储记录流；
- 实时记录流；

除了消息系统外，Kafka也可作为存储系统或流处理器使用。

## 2 基本概念

### 基本组件

KafKa系统中的基本角色有三个：

- Producer：发布消息的客户端（生产者）；
- Broker：从生产者接收并存储消息，提供给消费者读取；
- Consumer：从Broker中读取消息的客户端（消费者）；

### 核心API

Kafka 有四个核心API，分别是：

- Producer API，允许应用程序向一个或多个 topics 上发送消息记录；
- Consumer API，允许应用程序订阅一个或多个 topics 并处理为其生成的记录流；
- Streams API，允许应用程序作为流处理器，从一个或多个主题中消费输入流并为其生成输出流，有效的将输入流转换为输出流；
- Connector API，允许构建和运行将Kafka连接到现有的应用程序或数据系统的可用生产者和消费者；

### 基本概念

- **Topic**

  主题。Kafka使用一个类别属性来划分消息的所属类，划分消息的这个类称为Topic。Topic相当于消息的分配标签，是一个逻辑概念。

- **Partition**

  分区。Topic中的消息被分割为一个或多个Partition，Partition是一个物理概念，对应到系统上就是一个或若干个目录，一个分区就是一个提交日志。消息以追加的形式写入分区，先后以顺序的方式读取。

  由于一个主题可以包含数个分区，因此无法保证整个Topic是有序的，但是单个Partition分区可以保证有序。Kafka通过分区来实现数据冗余和伸缩性。

  分区可以分布在**不同的服务器**上，也就是说，一个Topic可以跨越多个服务器，以此来提供比单个服务器更强大的性能。

- **Segment**

  段。Partition进一步被细分为若干个Segment，每个Segment文件的大小相等。

- **Broker**

  Kafka集群包含一个或多个服务器，服务器被称为Broker。Broker接收来自生产者的消息，为消息设置偏移量offset，并提交消息到磁盘保存。Broker为消费者提供服务，对读取分区的请求作出响应，返回已经提交到磁盘上的消息。

- **Producer**

  生产者，即消息的发布者。负责将某Topic的消息发布到相应的Partition中。生产者在默认情况下把消息均衡地分布到主题的所有分区上，不关心特定消息会被写到哪个分区。

- **Consumer**

  消费者，即消息的使用者。一个消费者可以消费多个Topic的消息，对于某一个Topic的消息，其只会消费同一个Partition中的消息。

- **Controller**

  控制器，Kafka中的全局协调者。控制器本身也是一个普通的Broker，但需要负责一些额外的工作，如：

  - 监听Topic的变化，创建、删除Topic；
  - 集群Broker管理：新增 Broker、Broker 主动关闭、Broker 故障等；
  - 从Zookeeper中读取获取当前所有与主题、分区及broker有关的信息并进行相应的管理；
  - 启动并管理分区状态机和副本状态机。
  - 更新集群的元数据信息；

  Kafka集群中始终只有一个Controller。

## 3 Kafka的Leader Election方案

由于一个Partition可以分布在多个服务器上，Kafka需要对一个Partition对应的服务器集群进行Leader选举。分为两步：

1. 从所有Broker中选举出一个Controller：

   Kafka中的控制器选举工作依赖于Zookeeper，成功竞选成为控制器的broker会在Zookeeper中创建一个临时节点`/controller`，例如：

   ```sql
   {"version":1,"brokerid":0,"timestamp":"1593330804078"}
   ```

   - version：与Kafka版本相关，对同一个Kafka版本来说为固定值。
   - brokerid：表示成为控制器的broker的id编号。
   - timestamp：表示竞选成为控制器时的时间戳（精确到毫秒）。

   在任意时刻，集群中有且只有一个控制器。每个broker启动的时候会去尝试读取`/controller`节点的brokerid的值，如果读取到的brokerid的值不为-1，表示已经有其他broker节点成功竞选为控制器，当前broker就会放弃竞选；如果Zookeeper中不存在`/controller`节点，或者这个节点的数据异常，那么就会尝试去创建`/controller`节点。当前broker去创建节点的时候，也有可能有其他broker同时去尝试创建这个节点，只有创建成功的那个broker才会成为控制器。每个broker都会在内存中保存当前控制器的brokerid值，这个值可以标识为activeControllerId。

   此外，Zookeeper中还有一个与控制器有关的`/controller_epoch`节点，这个节点是持久（Persistent）节点，节点中存放的是一个整型的controller_epoch值，用于记录控制器发生变更的次数，即记录当前的控制器是第几代控制器，也可以称之为控制器纪元。

   controller_epoch的初始值为1，即集群中的第一个控制器的纪元为1，当控制器发生变更时，每选出一个新的控制器就将该字段值加1。每个和控制器交互的请求都会携带controller_epoch这个字段。如果请求的controller_epoch值小于内存中的controller_epoch值，则认为这个请求是向已经过期的控制器发送的请求，该请求会被认定为无效；如果请求的controller_epoch值大于内存中的controller_epoch值，那么说明已经有新的控制器当选了（也就是说接收到这种请求的broker已经不再是控制器了）。Kafka通过controller_epoch来保证控制器的唯一性（**避免脑裂问题**），进而保证相关操作的一致性。
   当`/controller`节点被删除时，每个broker都会进行选举，如果broker在节点被删除前是控制器，那么在选举前还需要有一个“退位”的动作，关闭相应的资源，如关闭状态机、注销相应的监听器等。而`/controller`节点的数据发生变化后，每个broker都会更新自身内存中保存的activeControllerId。

2. 