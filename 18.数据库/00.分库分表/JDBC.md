数据库分库分表分为水平分库分表，垂直分库分表。垂直分库分表主要是按照业务拆分独立库。水分分库分表主要是按照

## 水平分库分表目的。

水平分库分表主要能解决：分库可以解决单点访问量及索引IO。分表可以解决索引大量IO。分表的优势可以利用本地事务。数据尽量无状态，这样方便拆分。

分库和分表均可以有效的避免由数据量超过可承受阈值而产生的查询瓶颈。 除此之外，分库还能够用于有效的分散对数据库单点的访问量；分表虽然无法缓解数据库压力，但却能够提供尽量将分布式事务转化为本地事务的可能，一旦涉及到跨库的更新操作，分布式事务往往会使问题变得复杂。

## 水平分库分表原理

主要是解析sql，根据sql转化为多库查询sql查询不同的库，然后进行汇总。数据量还是有限的情况下才可以这样。

### 概念

+ SQL

  + 逻辑表：水平拆分数据表相同逻辑的总成

  + 真是表：真实的物理表

  + 数据节点：数据源名称和数据表组成

  + 绑定表：指定分片规则的主表和子表，这样不会出现笛卡尔积查询。

  + 广播表：指所有的分片数据源中都存在的表，例如：字典表。

  + 逻辑索引：某些数据库（如：PostgreSQL）不允许同一个库存在名称相同索引，逻辑索引用于同一个库不允许出现相同索引名称的分表场景，需要将同库不同表的索引名称改写为`索引名 + 表名`，改写之前的索引名称成为逻辑索引。

+ 分片

  + 分片键：支持多字段进行分片
  + 分片算法：
    + 精确分片算法：= IN，用于单键
    + 范围分片算法：BETWEEN  AND，用于单键
    + 复合算法：用于多键分片算法
    + Hint分片算法
  + 分片策略
    + 标准分片策略：RangeShardingAlgorithm是可选的，用于处理BETWEEN AND分片，如果不配置RangeShardingAlgorithm，SQL中的BETWEEN AND将按照全库路由处理。
    + 复合分片策略：对应ComplexShardingStrategy。复合分片策略。提供对SQL语句中的=, IN和BETWEEN AND的分片操作支持。ComplexShardingStrategy支持多分片键，由于多分片键之间的关系复杂，因此并未进行过多的封装，而是直接将分片键值组合以及分片操作符透传至分片算法，完全由应用开发者实现，提供最大的灵活度。
    + 行表达式分片策略：对应InlineShardingStrategy。使用Groovy的表达式，提供对SQL语句中的=和IN的分片操作支持，只支持单分片键。对于简单的分片算法，可以通过简单的配置使用，从而避免繁琐的Java代码开发，如: `t_user_$->{u_id % 8}` 表示t_user表根据u_id模8，而分成8张表，表名称为`t_user_0`到`t_user_7`。
    + Hint分片策略：对应HintShardingStrategy。通过Hint而非SQL解析的方式分片的策略。
    + 不分片策略
  + Hint

对于分片字段非SQL决定，而由其他外置条件决定的场景，可使用SQL Hint灵活的注入分片字段。例：内部系统，按照员工登录主键分库，而数据库中并无此字段。SQL Hint支持通过Java API和SQL注释(待实现)两种方式使用。

+ 配置

  可以配置的内容

  + 分片规则
  + 数据源配置
  + 表配置
  + 数据节点配置
  + 分片策略配置
    + 数据源分片策略
    + 表分片策略
  + 自增主键生成策略

### 内核分析

#### 解析引擎

#### 路由引擎

根据分片键的不同可以划分为单片路由(分片键的操作符是等号)、多片路由(分片键的操作符是IN)和范围路由(分片键的操作符是BETWEEN)。 不携带分片键的SQL则采用广播路由。

**路由策略主要是根据分片规则拆分成不同sql语句**。主要策略是根据逻辑表拆分成真实表进行查询。路由引擎主要分为：

+ 分片路由：用于根据分片键进行路由的场景，又细分为直接路由、标准路由和笛卡尔积路由这3种类型

  + 直接路由：Hint方式分片，过Hint（使用HintAPI直接指定路由至库表）方式分片，并且是只分库不分表的前提下，则可以避免SQL解析和之后的结果归并。 因此它的兼容性最好，可以执行包括子查询、自定义函数等复杂情况的任意SQL。直接路由还可以用于分片键不在SQL中的场景。读写分离，满足复杂sql

  + 标准路由：适用范围是不包含关联查询或仅包含绑定表之间关联查询的SQL。当分片运算符是等于号时，路由结果将落入单库（表），当分片运算符是BETWEEN或IN时，则路由结果不一定落入唯一的库（表），因此一条逻辑SQL最终可能被拆分为多条用于执行的真实SQL。非关联查询，不会产生笛卡尔积路由。

  + 笛卡尔积路由：它无法根据绑定表的关系定位分片规则，因此非绑定表之间的关联查询需要拆解为笛卡尔积组合执行。 如果上个示例中的SQL并未配置绑定表关系，那么路由的结果应为：

+ 广播路由（不带分片键）

  + 全库路由：库设置的SET类型的数据库管理命令，以及TCL这样的事务控制语句
  + 全库表路由：数据库中与其逻辑表相关的所有真实表的操作，主要包括不带分片键的DQL和DML（Insert，UPDATE，DELETE），以及DDL
  + 全实例路由：用于DCL（数据控制语言，权限）操作，授权语句针对的是数据库的实例。无论一个实例中包含多少个Schema，每个数据库的实例只执行一次。
  + 单播路由：获取某一真实表信息的场景

#### 改写引擎

+ 对于排序，聚合需要查出来后进行归并，所以需要课外添加字段，进行归并
+ 求平均值时，需要改为sum和count后，在归并引擎计算。
+ 分页修正：在分页时，需要从头查询到个数，然后汇聚截取，这个最好根据分页顺序进行作为筛选条件。减少路由计算。
+ 优化改写：
  + 单节点优化：将所有信息都在一个表中查询，不存在优化
  + 流式归并优化：在sql后增加order by 这样可以进行流式归并

#### 执行引擎

分为两种模式：

+ 内存限制模式：有请求创建连接，可以做流式归并优先
+ 连接限制模式：每个数据库实例创建一个，内存优先模式

通过maxConnectionSizePerQuery ，需要的查询数/maxConnectionSizePerQuery 如果小于等于1 使用内存限制模式，否则使用连接限制模式。

#### 归并引擎

+ 结果归并从功能上分为遍历、排序、分组、分页和聚合5种类型，它们是组合而非互斥的关系。

+ 从结构划分，可分为流式归并、内存归并和装饰者归并。
+ 流式归并引擎：主要思想是排序，纵向数据库自带排序，横向使用compare接口排序。

+ 遍历归并：遍历组成单向链表，遍历一张表后合并再遍历另一张表
+ 排序归并：根据游标当前值放入优先级队列，根据数值，对当前数值进行比较。
+ 分组归并：根据游标当前值放入优先级队列，把相同的聚合
+ 聚合归并：区分MAXMINSUMCOUNT AVG就是SUM及COUNT
+ 分页归并：流式归并不会占用内存，内存会占用大，尽量采用分片键ID作为排序。



### 书写规范

#### 路由至单数据节点

100%兼容

#### 路由至多数据节点

+ 支持一层子查询，用于聚合分页使用，子查询中不能有聚合。
+ 支持select 聚合，查询，分页
+ 支持联表查询
+ 支持insert update delete，创建索引，建表等操作，支持DISTINCT，但不支持聚合函数DISTINCT

#### 分页子查询

+ Oracle 支持rownum进行分页



### 行表达式

+ 解决分片规则配置繁琐，书写每个源麻烦
+ 分片算法通过表达式书写，可以统一存放

#### 配置数据节点

```
db0.t_order${0..1},db1.t_order${2..4}
```

#### 配置分片算法

```
ds${id % 10}
```

### 默认分布式主键生成器

#### 雪花算法

+ 同义毫秒生成4096个数据
+ 可以自增
+ 单机执行
+ 组成0+41时间戳+机房设备id（10）+自增数据（12）
+ 时间回退，当时间少，等待时间到达，时间多时跳出异常。
+ 时钟回拨：DefaultKeyGenerator.setMaxTolerateTimeDifferenceMilliseconds()

### 强制分片路由

有些分片路由可以不根据sql，而是外部指定，这个叫做Hint

## 读写分离

### 概念

+ 主库：提供添加、更新以及删除操作所使用的数据库
+ 从库：查询数据操作所使用的数据库
+ 主从同步：数据库支持
+ 负载均衡策略

## 核心功能

+ 提供一主多从的读写分离配置，可独立使用，也可配合分库分表使用
+ 独立使用读写分离支持SQL穿透
+ 同一线程且同一数据库连接内，如有写操作，以后的读操作均从主库读取，用于保证数据一致性。
+ 基于Hint的强制主库路由

## 数据治理

### 配置中心

+ 配置集中化：
+ 配置动态化：

#### 配置中心数据结构

配置中心在定义的命名空间的config下，以YAML格式存储，包括数据源，数据分片，读写分离、ConfigMap及Properties配置，可通过修改节点来实现对于配置的动态管理。

```
config
    ├──authentication                # Sharding-Proxy权限配置
    ├──configMap                     # 数据分片ConfigMap配置，以K/V形式存储，如{"key1":"value1"}
    ├──props                         # 属性配置
    ├──schema                        # Schema配置
    ├      ├──sharding_db            # SchemaName配置
    ├      ├      ├──datasource      # 数据源配置
    ├      ├      ├──rule            # 数据分片规则配置
    ├      ├──masterslave_db         # SchemaName配置
    ├      ├      ├──datasource      # 数据源配置
    ├      ├      ├──rule            # 读写分离规则
```

+ config/authentication
+ config/configMap:读写分离ConfigMap配置
+ config/sharding/props 相对与sharding-sphere配置里面的sharding Properties
+ config/schema/sharding_db/rule

```
tables:
  t_order:
    actualDataNodes: ds_$->{0..1}.t_order_$->{0..1}
    databaseStrategy:
      inline:
        algorithmExpression: ds_$->{user_id % 2}
        shardingColumn: user_id
    keyGeneratorColumnName: order_id
    logicTable: t_order
    tableStrategy:
      inline:
        algorithmExpression: t_order_$->{order_id % 2}
        shardingColumn: order_id
  t_order_item:
    actualDataNodes: ds_$->{0..1}.t_order_item_$->{0..1}
    databaseStrategy:
      inline:
        algorithmExpression: ds_$->{user_id % 2}
        shardingColumn: user_id
    keyGeneratorColumnName: order_item_id
    logicTable: t_order_item
    tableStrategy:
      inline:
        algorithmExpression: t_order_item_$->{order_id % 2}
        shardingColumn: order_id
bindingTables:
  - t_order,t_order_item
broadcastTables:
  - t_config
  
defaultDataSourceName: ds_0
```

+ config/schema/masterslave/rule

  ```
  name: ds_ms
  masterDataSourceName: ds_master 
  slaveDataSourceNames:
    - ds_slave0
    - ds_slave1
  loadBalanceAlgorithmType: ROUND_ROBIN
  ```

#### 动态生效

在注册中心上修改、删除、新增相关配置，会动态推送到生产环境并立即生效。

### 编排治理

可以对注册的实例启用，停用。在注册中心上使用。

+ state/instatces：数据库访问对象的运行实例，由IP和PID构成，治理运行中实例对数据库的访问
+ state/datasources：治理读写分离从库

### 支持的注册中心

### 应用性能监控



## 数据库设计

数据库分库分表设计时，需要考虑扩容。分库分表策略有分段（按照ID段进行）和取模

分段：容易扩容，通过ID递增每500W一个实例，但是容易造成热点数据库的问题

取模：数据分散但是不容易取模。

取模+分段：分库取模+每个实例分段，这个会解决两者。

[MySQL数据库水平扩容方案-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/578120)

