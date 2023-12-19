## 测试工具

```
# 测试一：100个并发连接，100000个请求，检测host为localhost 端口为6379的redis服务器性
能
redis-benchmark -h localhost -p 6379 -c 100 -n 100000
# 测试出来的所有命令只举例一个！
====== SET ======
100000 requests completed in 1.88 seconds # 对集合写入测试
100 parallel clients # 每次请求有100个并发客户端
3 bytes payload # 每次写入3个字节的数据，有效载荷
keep alive: 1 # 保持一个连接，一台服务器来处理这些请求
17.05% <= 1 milliseconds
97.35% <= 2 milliseconds
99.97% <= 3 milliseconds
100.00% <= 3 milliseconds # 所有请求在 3 毫秒内完成
53248.14 requests per second # 每秒处理 53248.14 次请求
```

## 常用指令

```shell
# select指令切换
select 7
# Dbsize 查看当前数据库key的数量
DBSIZE
# key * 查看具体key
# flushdb 清空当前数据库
# flushall 清空所有数据库
```

### 数据类型

+ String数据类型
  + 一个value最大512M
+ Hash
  + 键值对集合
+ List
  + 按顺序插入，可以是头部也可以是尾部
  + 底层是个链表
+ Set
  + 无需集合
  + 用hash表存储，插入是计算hash值，存储到存储中
+ zSet
  + 有序唯一存储

### key值

```
# key * 查看所有key值
# exists key 判定key是否存在
# move key db
# expire key 秒钟：设置生存时钟
# ttl key 查看还有多长时间过期 -1 表示永不过期 -2 表示已过期
# type key 查看key的类型
```

### string 操作

```shell
# ==========================================================
# set、get、del、append、strlen
# ==========================================================
# 设置值
set key value
# 获得key的值
get key
# 删除key
del key
# 查看全部key
keys *
# 确定key是否存在
exists key
# append 对不存在key等同set 对存在key 追加
append key "hello" 
append key "200"
# 获取长度
strlen key

# ========================================================
# incr、decr    一定要是数字才能进行+1 -1
# incrby、decrby 命令将key中存储的数字加上指定
# getrange 获取指定区间范围内的值
# setrange 范围区间内修改值
# setex 设置键值过期时间  SETEX KEY_NAME TIMEOUT VALUE
# setnx 如果不存在就设置 setnx key value
# mset  联系设置多值
# mget  获得多个值
# msetnx 原子操作，成功返回1，否则返回0 原子性
# set user:1 value
# mset user:1 value user:2 value2
# getset 先get后set 如果为空则nil
getrange key 0 2
setrange key 1 xx



```

### List操作

用链表存储，查找慢

```shell
# =================
# Lpush :LPUSH key value
# Rpush : 尾部插入  RPUSH key value
# lrange：获得区间 -1 最后一个 -2 倒数第二个
# lpop：用于移除并返回第一个元素
# rpop：移除并返回最后一个元素
# lindex：按照索引获得元素
# Llen：返回列表长度
# Redis Lrem 根据参数 COUNT 的值，移除列表中与参数 VALUE 相等的元素。
# 	COUNT 的值可以是以下几种：
# 	count > 0 : 从表头开始向表尾搜索，移除与 VALUE 相等的元素，数量为 COUNT 。
# 	count < 0 : 从表尾开始向表头搜索，移除与 VALUE 相等的元素，数量为 COUNT 的绝对值。
# 	count = 0 : 移除表中所有与 VALUE 相等的值。
# Ltrim key 1 2 保留列表中指点区间的值，不在指定区间删除
# rpoplpush  列表弹出，插入
# lset key index value 
# linsert key before/after pivoit value 在pivoit之前插入value
```

### set

使用hash表存储

```shell
# ====================================================
# sadd
# smembers
# sismermbers
# scard 元素个数
# srem 删除成员
# srandmember 返回随机值
# spop 移除一个元素
# smove 从一个集合移动到另一个集合
# sdiff 差集
# sinter 交集
# sunion 并集
```

### hash表

value 为kv值

```shell
# ===================================================
# hset
# hget
# hmset
# hmget
# hgetall
# hdel
# hlen
# hexists
# hkeys
# hvals
# hincrby
# hsetnx 为不存在的key赋值
```

### 有序集合

```shell
# ========================================
# zadd
# zrange
# zrangebyscore
# zrem
# zcard
# zcount 指定区间的数量
# zrank 排名
# zrevrank 返回有序成员中的排名
```

### 地理位置

底层使用zset实现，使用zset所有指令都可以使用

```shell
# geoadd key longitude latitude  value 添加经纬度
# geopos key  value 获得位置经纬度
# geodist 获得两个value的距离
# georadius 获得圆心半径中的值
# georadiusbymember
# 
```

### HyperLogLog

统计基数，原数中不重复数的个数

```shell
# PFADD key elment 添加
# PFCOUNT key 统计
# PFMERGE destkey key1 key2 合并 
```

### bitmap

底层使用字符串操作，只不过存储空间为bit位，占用空间少

```shell
# setbit key offset value 
# getbit key  offset
# bitcount key [start,end] 统计key的个数
```

## Redis持久化

## 事务

## 订阅发布

## 穿透和雪崩

## Jedis

## Springboot



