
## InnoDB VS MyISAM
+ InnoDB支持ACID事务4个特性
+ InnoDB支持行级别的锁粒度，MyISAM只支持表级
+ InnoDB支持事务4种隔离级别，默认是可重复读repeatable read
+ InnoDB支持crash安全恢复
+ InnoDB支持外键
+ InnoDB支持MVCC（多版本控制）
+ InnoDB聚集索引
### 应用场景
+ MyISAM管理非事务表，提供高速存储和检索，以及全文搜索能力。
+ InnoDB用于事务

## 索引
### 索引结构
哈希、B树、B+树
+ hash：hashmap用于等值查询
+ B树平衡多路查找数据
+ B+数据，非子叶结点只存储键值信息
#### B+树特点
+ 支持范围查询
+ 具备排序功能
#### InnoDB存储大小
InnoDB存储引擎为16KB，如果INT或者BIGINT类型能够存储1K键值，3层的话为10亿条数据，一般B+树高度为2~4层，找到指定数据为1~3次IO。

### 索引分类
#### 物理分类
+ 聚集索引，主索引的子页结点存放整张表的行记录
+ 非聚集索引：物理存储不是连续的，通过主键
#### 逻辑分类
+ 主键索引：不允许为NULL
+ 唯一索引：不允许重复，允许为NULL
+ 普通索引：允许为NUL，允许重复
### 最左匹配原则、覆盖索引、索引下推
+ 最左匹配原则：简历联合索引最左优先，范围查询就是终点，后续不能在进行索引。
+ 覆盖索引：索引的数据已经select包含的列，不用回表。
+ 索引下推：优先查询索引值，减少回表。
### 联合索引好处
+ 减少开销
+ 覆盖索引
+ 筛选效率高
+ 有序
## EXPLAIN
### select_type 列
+ 简单查询：不包含子查询和union
+ 复杂查询：简单子查询、派生表、union
### table列，访问哪个表
### type列，关联类型，MySQL决定如何查找表中的杭
system > const > eq_ref > ref > range > index > ALL
### filterd列 ，满足查询记录的比例
filterd越低说明利用索引索引越少
### Extra列表
+ Using index : 列被索引覆盖，不用回表
+ Using where ：查询的where条件列未被索引覆盖
+ Using filesort ： 排序没有使用索引
+ NULL : 查询的列未被索引
+ Useing index condition：查询的列不完全被索引覆盖

## 插入数据
插入数据时，事务没有关闭会创建新的聚集索引和非聚集索引，根据版本号进行管理。

# MVCC
MVCC时多版本控制，在创建事务时，对数据添加事务id，这样无法看到之后开的事务的数据操作，做到可重复读。不过在遇到update、insert、delete、for update 为当前读，会将当前版本事务号设为A的事务号，所以就会读到update修改多的数据，这块需要谨慎操作。
+ ReadCommited隔离级别：每次select 都生成一个快照读
+ ReadRepeatable：开启事务后第一个select语句才是快照读的地方，而不是一开启事务就快照都
+ RR级别下，快照读时通过MVVC（多版本控制）来实现，当前读时通过record lock（记录锁）和gap lock（间隙锁）来实现。



 生成表级共享锁，允许其它线程读取数据但不能修改数据

# 锁
加锁是为了防止其他事务同时操作
按锁的粒度分为：全局锁、表级锁、行锁、间隙锁、临间锁
按锁的属性：共享锁、排他锁
## 全局锁
对整个数据库加锁。Flush tables with read lock
## 表级锁
## 行级锁
+ 共享锁：当前读模式，开启多事务可以共享数据读，但是不能修改
+ 排他锁：当前读模式，只允许这个事务进行操作
+ 间隙锁：在RR模式有，在进行当先读时，周边范围id也进行上锁。

# 关联查询
关联查询首先先查询一张表数据，然后循环查询另一张数据，如果筛选条件，索引生效的是第一张表。

# Order by 工作原理
+ sort_buffer : 拿到数据，在内存中排序
+ rowid：内存不够，使用rowid记录文件位置，影响性能
# count工作原理


+ [mysql基础原理](https://juejin.cn/post/7127243755249205279)
+ [mvcc解决幻读](https://www.cnblogs.com/xuwc/p/13873293.html)
+ [数据修改，索引变化的原理](https://www.zhihu.com/question/27674363)
+ [事务级别](https://zhuanlan.zhihu.com/p/117476959)
+ [索引建立优化](https://zhuanlan.zhihu.com/p/117476959)
+ [关于连接检索过程](https://blog.csdn.net/zhou307/article/details/104158664)
+ [mysql知识清单](https://pdai.tech/md/db/sql-mysql/sql-mysql-mvcc.html)