# Redis

## 介绍

Redis(Remote Dictionary Server),远程字典服务 。

开源的使用C语言编写的，支持网络，可基**于内存亦可持久化的日志型数据库**，提供多语言API，是**最热门的NoSQL之一**

**高性能的**key-value数据库，为了保证效率，数据都是**缓存在内存中**。区别的是redis会**周期性**的把更新的数据**写入磁盘**或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

读的速度：110000次/s，写的速度：81000次/s

Redis支持**主从同步**。数据可以从主服务器向任意数量的从服务器上同步，从服务器可以是关联其他从服务器的主服务器。

### 什么是NoSQL

> 背景

传统的关系数据库在处理web2.0网站，特别是超大规模和高并发的[SNS](https://baike.baidu.com/item/SNS/10242)类型的web2.0纯[动态网](https://baike.baidu.com/item/动态网)站已经显得			力不从心 



 NoSQL数据库的产生就是为了解决大规模数据集合多重数据种类带来的挑战，尤其是大数据应用难题 

> 区别

- **关系型数据库**
  - 事先建立数据库字段（数据库设计）
  - ACID
  - 数据和关系都在表中



- 非关系型数据库
  - 牺牲ACID换取高性能
  - 高性能，高可用，高可扩





> 优点

-  **易扩展** ： 去掉关系数据库的关系型特性。数据之间无关系，这样就非常容易扩展
-  **大数据量，高性能**：非常高的读写性能，尤其在大数据量下，同样表现优秀 
- **灵活的数据模型**： 无须事先为要存储的数据建立字段，随时可以存储自定义的数据格式 
- **高可用**： NoSQL在不太影响性能的情况，就可以方便地实现高可用的架构。



### 代表作品

==K-V型NoSQL==：Redis

以键值对存储数据

**优点**：最大优点高性能

- 数据基于内存，读写效率高
- KV型数据，时间复杂度为o（1），查询速度快

**缺点**

- 只能根据K查V，存储数据据缺少结构化
- 查询方式单一，只有KV方式，不支持条件查询，多条件查询浪费空间
- 内存有限，无法支持海量数据存储
- 基于内存，有丢失的风险

综上所述，K-V型NoSQL用于==缓存==的场景：读远多于写，读取能力强，读取可以容忍数据丢失



==搜索型NoSQL==：ElasticSearch

传统关系数据库主要通过索引达到快速查询的目的，但是全文搜索的场景下，like查询无法满足所有模糊查询需求，而且容易形成**慢查询**。

搜索型NoSQL为了解决全文搜索问题：原理是==倒排索引==

**优点**

- 支持分词场景，全文搜索，这是区别关系型数据库最大特点
- 支持条件查询，支持聚合操作，类似Group By，但是功能更加强大，适合做数据分析
- 集群环境下方便横向扩展

**缺点**

- 性能全靠内存，非常吃硬件资源
- 消耗大量内存
- 读写有延迟
- 数据结构灵活性不高，字段建立就没法修改类型

综上所述，搜索型NoSQL适用于==有条件搜索特别是全文搜索==，对分表之后的数据查询也非常强大



==列式NoSQL==：HBase

大数据时代最具代表性的计数之一

基于列式存储。：都有关系型数据库一样有主键概念

 ![img](redis.assets/801753-20190810184027372-913062087.png) 

，通过主键连接各个属性![1590161562309](redis.assets/1590161562309.png)

**优点**：

- 查询时只会读取指定的列，不会读取所有列，性能提高
- 海量数据无限存储，节约存储空间，null值不会被存储，当一列中有很多重复数据，可将其压缩
- 横向扩展是最方便的质疑，只要添加新机器就可以实现数据存储的现行增长，节省成本

**缺点**

- KV式，不支持条件搜索（很弱）
- 不支持分页查询

综上所述，列式NoSQL比较适合==K-V型且无法预估数据增长量==的场景



==文档型NoSQL==：MongoDB

将半结构化数据存储为结构的一种NoSQL，通常通过JSON过XML存储，没有Schema，可以随意存储和读取数据，用于==解决关系型数据库表结构扩展不方便的问题==。

![1590162050881](redis.assets/1590162050881.png)

使用index同样可以建立索引，且效率大大提高

**优点**

- 没有预定义字段，扩展字段容易
- 读写性能优越

**缺点**

- 不支持事务操作
- 不支持多表关联查询
- 空间张永达
- 运维工具少（例如navicat）

综上所述，==对标关系型数据库，比较适合处理没有多表查询，没有强一致性且表经常变化==的场景

 ![img](https://img2018.cnblogs.com/blog/801753/201908/801753-20190812094840816-785471233.png) 

 ![img](redis.assets/801753-20190812094840816-785471233.png) 

> 使用场景

1、数据模型比较简单

2、需要灵活性更强的IT系统

3、对数据库性能要求较高

4、不需要高度的数据一致性

5、对于给定key，比较容易映射复杂值的环境


# 语句

## key键命令
### keys
查看所有key值的value
`keys *`
![20200824171325](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824171325.png)

### del
删除key命令，如果存在该key，就返回1，不存在就返回0
`del key`
如图，存在key-name，删除成功返回1，不存在key-yy，删除失败返回0
![20200824103328](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824103328.png)

### exists
判断key是否存在,存在返回1，不存在返回0
`exists key`
![20200824104922](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824104922.png) 

### 设置过期时间
给定key设置过期时间 
过期之后，key不存在
- pexpire
单位毫秒
`pexpire key time`

- expire
单位秒
`expire key time`

- ttl
查看key还有多少秒过期
`ttl key`


### type
查看key的值的类型
`type key`
![20200824170943](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824170943.png)


## 字符串命令
- **set**
  设置k-v

  ```bash
  set key value
    设置name的值为"yzy"
  set name "yzy"
  ```
  ![20200824102059](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824102059.png)
- **get**
  根据key获取value

  ```bash
  get key
  获取name的值
  get name
  ```
- **getset**
  将key存储的值改成newValue，并返回oldValue
  **如果没有key，返回nil**
  `getset key newValue`
  `return oldValue`

  ![20200824174324](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824174324.png)
- **append**
  指定key的value后面加上value，**返回添加后的总长度**
  `append key value`

-  **getrange**
  获取指定key的子字符串,从start-end
  `getrange key start end`
  ![20200824172316](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824172316.png)

- **mget**
  返回一个或多个key的值
  `mget key1 key2 ....`
  ![20200824174904](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824174904.png)

- **mset**
  设置多个key的值，如果已存在就覆盖
  `mset key1 value1 key2 value2 ....`
  ![20200824175052](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824175052.png)

- **msetnx**
  设置多个key的值，当且仅当**key都不存在**
  **原子性**，当一个不成立，全部都不执行
  `msetnx key1 value1 key2 value2 ....`
  ![20200824175702](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824175702.png)

- **decr/incr**
  使key存储的值-1/+1
  `decr/incr key`


- **decrby/incrby**
  使key存储的值-/+指定的值
  `decrby/incrby key value`
  ![20200824173530](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824173530.png)
  

# 安装和下载

## linux下载安装redis

1.打开官网下载stable

2.xftp传输到服务器

3.解压缩

tar -xvf redis-6.0.1.tar.gz

4.进入redis，make**编译**

```
cd redis-6.0.1

make
```

5.如果报错，可能是没有**安装gcc**，redis需要c实现

yum install -y gcc g++ gcc-c++ make

如果还是无法编译，升级gcc版本

```bash
yum -y install centos-release-scl  # 升级新版本 

yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils

scl enable devtoolset-9 bash

#如果想要长期使用高版本
 echo "source /opt/rh/devtoolset-9/enable" >>/etc/profile
```

6.再次编译

![image-20200720161257042](Redis.assets/image-20200720161257042.png)

7.进行**测试**

make test，发现出错

yum install tcl

再次测试成功

![image-20200720161834742](Redis.assets/image-20200720161834742.png)

8.在src目录下**启动redis**

./redis-server

![image-20200720162057641](Redis.assets/image-20200720162057641.png)

9.返回redis目录，修改配置文件，改为**后台启动**

vim redis.conf     

将daemonize no改成 yes

![image-20200720162554755](Redis.assets/image-20200720162554755.png)

10.**查看目前运行redis进程**

ps -aux|grep redis| grep -v grep

![image-20200720163928755](Redis.assets/image-20200720163928755.png)

**关闭**

- 杀死进程
  - kill -9 30179
- shutdown
  - 在使用redis时输入shutdown

11.在**src目录下**执行redis-cli**使用redis**

./redis-cli

![image-20200720165337217](Redis.assets/image-20200720165337217.png)



## windos下安装redis

1.在github上下载reis的windows安装包

2.解压缩

3.打开redis-server（powershell下与linux相同，为.\redis-server）

![image-20200810143205258](Redis.assets/image-20200810143205258.png)

4.如果想使用指定的配置文件，就在启动命令后面加上配置文件名

例如，指定的配置文件为redis.windows.conf

`.\redis-server redis.windows.conf` 




### 遇到的问题

运行项目时**报错**：连接redis错误：ERR Client sent AUTH, but no password is set

redis没有设置连接密码，但是项目连接数据库时发送了用户验证，导致冲突

**解决方案1**.项目发送用户请求时取消密码，删除配置文件中的密码

​						**失败**，删除密码会导致项目运行报错，没有设置密码

**解决方案2**：redis配置文件设置密码，与项目的连接密码相同

​						由于redis的配置文件没有显示出来，所以自己手动指定配置文件

​						redis.windows.conf文件中添加`requirepass 连接密码`

​						知道那个目录下cmd输入redis-server redis.windos.conf     

​					**成功**