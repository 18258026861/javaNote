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

---


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

---


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


---


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

## 启动关闭redis
### 开启
1.在redis的src目录下使用命令行打开redis服务端
`./redis -server`
2.src目录下打开redis-cli客户端
`./redis-cli`
3.ping检测redis是否启动
![20200825152404](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200825152404.png)


### 关闭
1.意外关闭，该情况下redis的数据不会保存
`ps -ef | grep -i redis`查看当前进程
`kill -9 进程号`关闭进程
![20200825164141](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200825164141.png)
2.正常关闭，数据会保存
在客户端命令行输入shutdown
![20200825164618](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200825164618.png)
再次启动访问时发现数据还保存了
![20200825164930](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200825164930.png)


## redis配置文件
```bash
redis.conf 配置项说明如下：
1. Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
    daemonize no

2. 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
    pidfile /var/run/redis.pid

3. 指定Redis监听端口，默认端口为6379，为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字
    port 6379

4. 绑定的主机地址
    bind 127.0.0.1

5. 当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
    timeout 300

6. 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
    loglevel verbose

7. 日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
    logfile stdout

8. 设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id
    databases 16

9. 指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
    save <seconds> <changes>
    Redis默认配置文件中提供了三个条件：
    save 900 1
    save 300 10
    save 60 10000
    分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。
 
10. 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
    rdbcompression yes

11. 指定本地数据库文件名，默认值为dump.rdb
    dbfilename dump.rdb

12. 指定本地数据库存放目录
    dir ./

13. 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
    slaveof <masterip> <masterport>

14. 当master服务设置了密码保护时，slav服务连接master的密码
    masterauth <master-password>

15. 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH <password>命令提供密码，默认关闭
    requirepass foobared

16. 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
    maxclients 128

17. 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
    maxmemory <bytes>

18. 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
    appendonly no

19. 指定更新日志文件名，默认为appendonly.aof
     appendfilename appendonly.aof

20. 指定更新日志条件，共有3个可选值： 
    no：表示等操作系统进行数据缓存同步到磁盘（快） 
    always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全） 
    everysec：表示每秒同步一次（折中，默认值）
    appendfsync everysec
 

21. 指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
     vm-enabled no

22. 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
     vm-swap-file /tmp/redis.swap

23. 将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
     vm-max-memory 0

24. Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值
     vm-page-size 32

25. 设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
     vm-pages 134217728

26. 设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4
     vm-max-threads 4

27. 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启
    glueoutputbuf yes

28. 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
    hash-max-zipmap-entries 64
    hash-max-zipmap-value 512

29. 指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）
    activerehashing yes

30. 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
    include /path/to/local.conf
```




## 内存策略
由于数据都存储在内存中，随着数据的不断增大，可能导致内存溢出，服务宕机，所以要设置内存策略，维护内存空间
### 1.设置超时时间
设置存入内存的数据的存活时间

常用`expire key time`设置存活时间（秒）


### 2.采用LRU算法 
>LRU指最近最少使用算法，对最近一段时间内最少使用的数据进行清理

1.volatile-lru：设定超时时间的数据中,删除最不常使用的数据.

2.allkeys-lru：查询所有的key中最近最不常使用的数据进行删除，这是**应用最广泛的策略**.






# 命令
redis支持**五种数据类型**：**string,hash,lis,set,zset**(sorted set：有序集合)。
key的**命名规范**：
- 不能太长，超过1024
- 不能太短，要便于理解
- 名称区分大小写
- 可以使用统一的命名规范来暗示数据之间的关系，统一使用：来分隔
  - 如class:06:cl 和class:06:yzy ,代表同为class：06的两个人，从命名上暗示了关系


## key键命令

```bash
#         keys    后面接正则表达式可以查询想要的结果
#         × 代表查询所有key
key *


#         del     删除命令
# 如果存在key，就返回1，不存在就返回0
del key


#         exists    判断是否存在
# 存在返回1，不存在返回0
exists key


#         tyep  查看key的值的类型
type key


#         rename  修改key名
rename oldKeyName new KeyName
```

![20200824171325](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824171325.png)


![20200824170943](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824170943.png)

### 设置过期时间
给定key设置过期时间 ,过期之后，key不存在

```bash
#      pexpire    单位毫秒
pexpire key time


#       expire      单位秒
expire key time


#       ttl   查看key还有多久过期
#  返回-1代表永久有效
ttl key


#       persist     取消过期时间
persist key
```

![20200825101043](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200825101043.png)

**过期时间应用场景**
- 限时的优惠活动
- 网站数据缓存（一些需要实时更新的数据，如排行榜）
- 手机验证码
- 展示网站访问频率

## 字符串类型

string数据结构是简单的K-V类型，value不仅可以是string，也可以是integer等其他的类型

string是二进制安全的即**string可以包含任何数据**

### 应用场景
- String通常用于保存单个字符串或JSON字符串
- String是二进制**安全的**，可以用来保存文件和图片
- **计数器**（自增自减）
  - incr指令具有**原子性**，同时有多个增加也能**正确得到结果**，不少网站利用redis这个特性来实现计数需求



### set
```bash
#   set  设置k-v
set key value
#设置name的值为"yzy"
set name "yzy"


#     mset
#设置多个key的值，如果已存在就覆盖
mset key1 value1 key2 value2 


#     setnx
#若key不存在则赋值并返回1，存在返回0不设值，用于解决分布锁的方案之一
setnx key value


#    msetnx
#设置多个key的值，当且仅当key都不存在
#原子性，当一个不成立，全部都不执行
msetnx key1 value1 key2 value2 ....

```
setnx的情况
 ![20200826163451](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200826163451.png)

**msetnx**的情况
  ![20200824175702](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824175702.png)






### get
```bash
# get    根据key获取value
get key
#获取name的值
get name


#     getrange
# 获取指定key的子字符串,从start-end,闭包区间
getrange key start end


#     mget
#返回一个或多个key的值
mget key1 key2 ....


#   getset   
#将key存储的值改成newValue，并返回oldValue
#如果没有key，返回nil
getset key newValue

```
getrange的范围
  ![20200824172316](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824172316.png)

mget的返回值
  ![20200824174904](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824174904.png)

getset的情况
  ![20200824174324](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824174324.png)


### append/strlen
  ```bash
  #       append
 #指定key的value后面加上value，返回添加后的总长度
  append key value


  #      strlen
  # 获取key存储的字符串长度
  strlen key
  ```


### 自增自减
```shell
# decr/incr   使key存储的值-1/+1
decr/incr key


#decrby/incrby  使key存储的值-/+指定的值
decrby/incrby key value
```
  ![20200824173530](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200824173530.png)

  


## Hash类型
**特别适合存储对象**，比存储在Strig中占用更少内存
每个hash可以存储2的32次方-1个键值对

### hset
```bash
# hset设置一个对象的一个属性
#hset 对象名 属性 值
hset user:1 name yzy 


# hmset 设置多个对象
#hmset 对象名 属性1 值 属性2 值。。。 
hmset user:3 name cl age 22 sex nan

# 
```


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


# 使用redisdesktopManager工具
1.安装完成

2.获取ip地址

ip地址从服务其输入ifconfig获取(错误)

![20200825173644](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200825173644.png)

**阿里云实例查看公网ip（正确）**

![20200826151400](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200826151400.png)

3.开放防火墙（无关紧要）

**查看防火墙端口**,`firewall-cmd --list-ports`
显示FirewallD is not running 

**开启防火墙**
`systemctl start firewalld`，
没有提示代表成功

**查看防火墙状态**`systemctl status firewalld`，看到running已运行

**开启端口**`firewall-cmd --zone=public --add-port=6379/tcp --permanent`,显示success

**重新加载防火墙**`firewall-cmd --reload`

**查看端口**`firewall-cmd --list-ports`


![20200825180059](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200825180059.png)

4.设置密码

将配置文件的密码注释取消`requirepass password`

使用配置文件开启redis服务`./redis-server ./redis.conf`,需要指定配置文件

![20200826143024](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200826143024.png)

![20200826144110](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200826144110.png)

5.新建连接

![20200825173532](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200825173532.png)

![20200826151626](https://cdn.jsdelivr.net/gh/18258026861/image@master/image/20200826151626.png)