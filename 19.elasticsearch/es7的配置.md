## es7 协调结点配置
```
# 新增两个属性，去掉zen的选举
discovery.seed_hosts: [] 定义通信的host
cluster.initial_master_nodes
```
es7 修改选举模式，使用一致性共识算法，使得选举master更迅速。

[es7 配置属性](https://www.infoq.cn/article/hefb1QQsWF2NHoRQ-tL0)