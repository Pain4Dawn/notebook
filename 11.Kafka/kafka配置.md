## consumer分区分配数据
在consumer加入consumer-group时，需要向broker读取分区及分配数据。这部分的流程大致是，当有consumer加入时，首先会在一个broker上作为coordinator（协调节点），然后让其返回相应的分区分配数据，主要包括该节点分区信息及对应的offset信息。
### offset等元数据信息都在一个broker
主要是因为存储__consumer_offsets信息都放到一个broker上，这个现象是因为当kafka还没有启动完时，就启动了消费者，所以没有均摊到所有broker上，当一个节点挂了记录这个节点信息就无法获得。解决方法通过修改
```
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1
```
### 挽救方法
在现场环境需要编辑一个json文件然后执行指令
```
kafka-reassign-partitions.sh --zookeeper localhost:2181 --reassignment-json-file reassign.json --execute
```
https://www.orchome.com/805
https://blog.csdn.net/fct2001140269/article/details/84137861   挽救措施
