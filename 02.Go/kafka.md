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