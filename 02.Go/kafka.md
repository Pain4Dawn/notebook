# golang kafka的使用

## 生产者

samara的的生产者，主要工作交互kafka服务端确定broker，partition中，确定好后发送消息给服务端

有两种发送方式，同步，异步。

+ 同步发送消息，每次发消息有返回值
+ 异步发送消息，需要建立error和success管道，消息通过管道来发送消息

## 消费者

消费者客户端状态图，ConsumerGroup有5种状态

+ Empty:无任何Consumer存在
+ Stable：完成Rebalance
+ PreparingRebalance：重新Balance
+ CompletingRebalance：所有成员均入组，各成员等待分配计划
+ Dead：Group生命周期结束

![group状态图](https://raw.githubusercontent.com/Pain4Dawn/notebook/master/98.picture/consumergroup%E7%8A%B6%E6%80%81%E5%9B%BE.jpg)

时序图

![时序图](https://raw.githubusercontent.com/Pain4Dawn/notebook/master/98.picture/consumerGroup%E6%B5%81%E7%A8%8B%E5%9B%BE.jpg)

groupRebalance主要是确定改消费者读取哪些broker上的partition。创建consumer时，会根据broker数创建brokerConsumer和partionConsumer。brokerConsumer为获得kafka服务器上broker的数据，partionConsumer获得指定partion的数据。brokerConsumer会根据broker建立个数，所以会是多个管道发送数据

![brokerConsumer与partitionConsumer结构图](https://raw.githubusercontent.com/Pain4Dawn/notebook/master/98.picture/brokerConsumer%E4%B8%8EpartitionConsumer.jpg)

![流程图](https://raw.githubusercontent.com/Pain4Dawn/notebook/master/98.picture/partitionConsumer%E6%B3%A8%E5%86%8CbrokerConsumer%E6%B5%81%E7%A8%8B%E5%9B%BE.jpg)

[sarama 源码学习 01：ConsumerGroup - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/109574627)

[Sarama 源码笔记 02：Consumer - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/110114004)



## Producer

producer 主要根据partion和broker建立协程，没有broker都与一个BrokerProducer相连接。PartitionProducer根据Broker注册到对应的BrokerProducer中。根据topic发送到不同的PartitionProduce内

有两个状态，只返回一个ack，一个所有数据都同步。



## 注意事项

+ kafka使用抓取，是为了解决消息速率不匹配的因素。
+ kafka抓取时，如果没有抓到时，会阻塞等待读取信息。









[Kafka(Go)教程(六)---sarama 客户端 producer 源码分析 | 指月小筑|意琦行的个人博客 (lixueduan.com)](https://www.lixueduan.com/post/kafka/06-sarama-producer/)