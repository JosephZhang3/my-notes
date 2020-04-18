
## 使用 set ex 命令设定过期时间（秒），使用ttl(time to live)命令查询k-v剩余存活时间

```
> set name zjh ex 20
OK
> get name
"zjh"
> ttl name
(integer) 15
> ttl name
(integer) 5
> get name
"zjh"
> get name
(nil)
```

***

## 使用 setnx 命令 可以实现分布式锁
#### 某个微服务copy到了多台机器上组成集群，某一时刻，只有一台机器上的微服务能成功插入特定key代表的k-v，其余所有机器都会失败。
#### 即 setnx 命令执行过程
检测到指定的key不存在时（Not Exist）才能插入，一旦发现存在就会放弃并返回0。
#### 而 set 命令执行策略
不检测指定的key的存在性，直接覆写value。当多个客户端同时插入相同key时，会不断覆写，以队列中最后一个客户端写入的值作为最终值。这不满足分布式锁的要求。
#### 为什么redis能实现分布式锁？
redis是单线程模型，因此不存在并发修改问题，不同客户端发送来的命令会被放到队列中依次执行，各个客户端都能正确看到其它客户端对key的修改情况。
```
> set name zjh ex 100
OK
> get name
"zjh"
> ttl name
(integer) 91
> setnx name zjh
(integer) 0
> setnx name turing
(integer) 0
```
***
## 字符串操作
strlen [key] 命令查询value长度

getrange [key] [beginIndex] [endIndex] 截取指定索引位置之间（包含起止索引）的字符串

```
> get name
"new nam"
> strlen name
(integer) 7
> getrange name 2 5
"w na"
```
***
## 订单ID生成方案
* auto increment
* UUID universal unique identification 全局唯一标识
* 雪花算法

***

## redis设计思路
first step:设计key，要求字符串短小精悍

second step:选择合适的数据结构，尽量不要存json字符串或者序列化后的二进制byte stream，不直观，而且转换处理巨麻烦

third step:书写文档，详细说明业务场景、设计意图、key缩写的含义、客户端操作数据结构的过程图解

***

## 购物车的设计实现
像淘宝这种用户体量的业务系统，不可能用数据库表来存放购物车数据。一是从硬盘IO速度慢，二是修改购物车是高频操作会占用大量数据库连接。所以，我们用内存数据库redis来实现购物车。

* first step:设计key，为了体现业务，我们使用 cart:id 表示购物车，prod[id] 表示购物车商品id

* second step:选择合适的数据结构 hash ，hmset命令的形式化结构如下，它很适合虚拟一个购物车
```
hmset [key] [property1-name] [property1-value] [property2-name] [property2-value] ...
```

* third step：如何操作数据结构来实现业务

```
> hmset cart:001 prod004 1 prod010 2
OK
> hgetall cart:001
1) "prod004"
2) "1"
3) "prod010"
4) "2"
> hincrby cart:001 prod010 2
(integer) 4
> hgetall cart:001
1) "prod004"
2) "1"
3) "prod010"
4) "4"
> hincrby cart:001 prod010 -1
(integer) 3
> hgetall cart:001
1) "prod004"
2) "1"
3) "prod010"
4) "3"
```

首先我们用hmset命令构造一个购物车，它包含两种商品，ID分别为 prod004 和 prod010 ，然后我们通过 hincrby 命令把其中一种商品 prod010 的购买数量增加2，或者减少1（使用负数）