# ZooKeeper使用场景

## 选举

### 非公平选举

不论等待时间，当没有leader时，大家同时争抢。大家在创建leader。谁创建了，谁就是leader。其他可以监听这个结点，如果删除的话，重新选举。

### 公平选举

创建临时顺序结点，谁是第一个谁为leader。其他监听上一个结点。如果上一结点变化则重新选择判定自己是否是第一个结点。







[使用Zookeeper实现选举 - 简书 (jianshu.com)](https://www.jianshu.com/p/87556c35d932)