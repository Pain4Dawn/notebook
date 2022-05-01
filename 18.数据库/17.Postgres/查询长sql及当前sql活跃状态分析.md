+ pg_stat_activity 为连接数据库的session信息。可以通过这张表查询有哪些session连接数据库。
+ 需要留意的属性有 state：目前sql的状态idle、in tranction、query 执行条件、pid 执行服务端的进程。
+ 关闭相关进程kill -9 pid
+ 查询正在执行语句session，一个session一个进程，可以根据session的开始查询查看当前查询sql的条件。

## 慢sql查询。

+ 编辑配置文件postgresql.conf  log_min_duration_statement = 200 大于200毫秒的操作将被记录到日志。查询postgres日志  分析sql

  

## explain

执行explain的sql，查询没有命中索引的语句。

[如何杀掉pg数据库正在运行的sql - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1555355)

[Postgresql 慢查询语句记录与分析 - SegmentFault 思否](https://segmentfault.com/a/1190000040711031)

[PgSQL · 最佳实践 · EXPLAIN 使用浅析（优化器，查询计划） - 雪球球 - 博客园 (cnblogs.com)](https://www.cnblogs.com/xueqiuqiu/articles/10999863.html)