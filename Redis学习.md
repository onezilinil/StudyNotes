# Redis学习

## 一、基本概念

### **1.Redis是什么**

Redis是用C语言开发的一个开源的高性能键值对（key-value）数据库。它通过提供多种键值数据类型来适应不同场景下的存储需求，目前为止Redis支持的键值数据类型如下：字符串类型、散列类型、列表类型、集合类型、有序集合类型。

### **2.NoSQL是什么**

NoSQL，泛指非关系型的数据库，NoSQL即Not-Only SQL，它可以作为关系型数据库的良好补充。随着互联网web2.0网站的兴起，非关系型的数据库现在成了一个极其热门的新领域，非关系数据库产品的发展非常迅速。而传统的关系数据库在应付web2.0网站，特别是超大规模和高并发的SNS类型的web2.0纯动态网站已经显得力不从心，暴露了很多难以克服的问题，例如： 

* **High performance - 对数据库高并发读写的需求：**web2.0网站要根据用户个性化信息来实时生成动态页面和提供动态信息，所以基本上无法使用动态页面静态化技术，因此数据库并发负载非常高，往往要达到每秒上万次读写请求。关系数据库应付上万次SQL查询还勉强顶得住，但是应付上万次SQL写数据请求，硬盘IO就已经无法承受了。其实对于普通的BBS网站，往往也存在对高并发写请求的需求，例如网站的实时统计在线用户状态，记录热门帖子的点击次数，投票计数等，因此这是一个相当普遍的需求。
* **Huge Storage - 对海量数据的高效率存储和访问的需求：**类似Facebook，twitter，Friendfeed这样的SNS网站，每天用户产生海量的用户动态，以Friendfeed为例，一个月就达到了2.5亿条用户动态，对于关系数据库来说，在一张2.5亿条记录的表里面进行SQL查询，效率是极其低下乃至不可忍受的。再例如大型web网站的用户登录系统，例如腾讯，盛大，动辄数以亿计的帐号，关系数据库也很难应付。 
* **High Scalability && High Availability- 对数据库的高可扩展性和高可用性的需求：** 在基于web的架构当中，数据库是最难进行横向扩展的，当一个应用系统的用户量和访问量与日俱增的时候，你的数据库却没有办法像web server和app server那样简单的通过添加更多的硬件和服务节点来扩展性能和负载能力。对于很多需要提供24小时不间断服务的网站来说，对数据库系统进行升级和扩展是非常痛苦的事情，往往需要停机维护和数据迁移，[为什么数据库不能通过不断的添加服务器节点来实现扩展呢](https://www.zhihu.com/question/26641240?sort=created)？ 

### **3.NoSQL的作用和分类**

NoSQL数据库的产生就是为了解决大规模数据集合多重数据种类带来的挑战，尤其是大数据应用难题。

一些主流的NOSQL产品：

![主流的NoSQL产品](.\Redis\主流的NoSQL产品.png) 

NoSQL数据库的四大分类如下：

| 数据库            | 相关产品                                           | 典型应用场景                                                 | 数据模型                                        | 优点                                         | 缺点                                                     |
| ----------------- | -------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------- | -------------------------------------------- | -------------------------------------------------------- |
| 键值（key-value） | Tokyo Cabinet/Tyrant, Redis, Voldemort, Oracle BDB | 内容缓存，主要用于处理大量数据的高访问负载，也用于一些日志系统等等。 | Key 指向 Value 的键值对，通常用hash table来实现 | 查找速度快                                   | 数据无结构化，通常只被当作字符串或者二进制数据           |
| 列存储数据库      | Cassandra, HBase, Riak                             | 分布式的文件系统                                             | 以列簇式存储，将同一列数据存在一起              | 查找速度快，可扩展性强，更容易进行分布式扩展 | 功能相对局限                                             |
| 文档型数据库      | CouchDB、MongoDB                                   | Web应用（与Key-Value类似，Value是结构化的）                  | 一系列键值对                                    | 数据结构要求不严格                           | 查询性能不高，而且缺乏统一的查询语法                     |
| 图形(Graph)数据库 | Neo4J、InfoGrid、Infinite Graph                    | 社交网络                                                     | 图结构                                          | 利用图结构相关算法                           | 需要对整个图做计算才能得出结果，不容易做分布式的集群方案 |

### **4.redis的应用场景**

* 缓存（数据查询、短连接、新闻内容、商品内容等等）。（最多使用）
* 分布式集群架构中的session分离。
* 聊天室的在线好友列表。
* 任务队列。（秒杀、抢购、12306等等）
* 应用排行榜。
* 网站访问统计。
* 数据过期处理（可以精确到毫秒）

> redis历史发展：
>
> 2008年，意大利的一家创业公司Merzia推出了一款基于MySQL的网站实时统计系统LLOOGG，然而没过多久该公司的创始人 Salvatore Sanfilippo便 对MySQL的性能感到失望，于是他决定亲自为LLOOGG量身定做一个数据库，并于2009年开发完成，这个数据库就是Redis。 不过Salvatore Sanfilippo并不满足只将Redis用于LLOOGG这一款产品，而是希望更多的人使用它，于是在同一年Salvatore Sanfilippo将Redis开源发布，并开始和Redis的另一名主要的代码贡献者Pieter Noordhuis一起继续着Redis的开发，直到今天。
>
> Salvatore Sanfilippo自己也没有想到，短短的几年时间，Redis就拥有了庞大的用户群体。Hacker News在2012年发布了一份数据库的使用情况调查，结果显示有近12%的公司在使用Redis。国内如新浪微博、街旁网、知乎网，国外如GitHub、Stack Overflow、Flickr等都是Redis的用户。
>
> VMware公司从2010年开始赞助Redis的开发， Salvatore Sanfilippo和Pieter Noordhuis也分别在3月和5月加入VMware，全职开发Redis。

### **5.redis的优缺点**

redis是将数据存放到内存中,由于内容存取速度快所以redis被广泛应用在互联网项目中,

* 优点：存取速度快，官方称读取速度会达到30万次每秒，写速度在10万次每秒最有,具体限制于硬件。
* 缺点：对持久化支持不够良好，所以redis一般不作为数据的主数据库存储，一般配合传统的关系型数据库使用。

[为什么分布式一定要有redis，redis的一些优缺点](https://blog.csdn.net/hcmony/article/details/80694560)

##  二、Redis安装、配置及运行

### **1.Redis运行环境**

* VM Workstation作为虚拟机；

* Centos6.4虚拟机作为Linux服务器；

* SecureCRT作为ssh客户端连接虚拟机；

* 安装Redis需要先将官网下载的源码进行编译，`yum install gcc-c++`安装gcc作为Redis编译依赖环境；

### **2.Redis安装**

* **源码下载：**从Redis[官网](http://download.redis.io/releases/redis-3.0.0.tar.gz)下载redis3.0，此版本主要增加了redis集群功能，将redis-3.0.0.tar.gz拷贝到Linux的/usr/local下
* **解压源码：**`tar -zxvf redis-3.0.0.tar.gz`，
* **编译：**进入解压后的目录进行编译，

```
cd /usr/local/redis-3.0.0

make
```

* **安装到指定目录：**如放在/usr/local/redis下，

```
cd /usr/local/redis-3.0.0

make PREFIX=/usr/local/redis install
```

* **配置文件：**redis.conf是redis的配置文件，redis.conf在redis源码目录。修改port作为redis进程的端口,port默认6379。
* **拷贝配置文件到安装目录下：**进入源码目录，里面有一份配置文件 redis.conf，然后将其拷贝到安装路径下，

```
cd /usr/local/redis

mkdir conf

cp /usr/local/redis-3.0.0/redis.conf  /usr/local/redis/bin`
```

* **安装目录bin下的文件列表：**

![bin下的文件列表](.\Redis\bin下的文件列表.png) 

文件说明：

| 文件名           | 说明                                              |
| ---------------- | ------------------------------------------------- |
| redis-server     | Redis服务器                                       |
| redis-cli        | Redis命令行客户端                                 |
| redis-benchmark  | Redis性能检测工具                                 |
| redis-check-aot  | AOF文件修复工具                                   |
| redis-check-dump | RDB文件检查工具                                   |
| redis-sentinel   | redis3.0新增的，是redis集群管理工具，可实现高可用 |

### **3.redis启动**

**3.1 前端模式启动**

直接运行bin/redis-server将以前端模式启动，前端模式启动的缺点是ssh命令窗口关闭则redis-server程序结束，不推荐使用此方法。如下图：

![前端模式启动](.\Redis\前端模式启动.png) 

**3.2 后端模式启动**

* 修改redis.conf配置文件， daemonize yes 以后端模式启动。

* 执行如下命令启动redis：

```
cd /usr/local/redis

./bin/redis-server ./redis.conf
```

* redis默认使用6379端口。

![后端模式启动](.\Redis\后端模式启动.png) 

也可更改redis.conf文件，修改端口号：

![后端模式启动2](.\Redis\后端模式启动2.png) 

**3.3 启动多个redis进程**

**3.1.1 **启动时指定端口可在一台服务器启动多个redis进程。

```
cd /usr/local/redis/bin

./redis-server ./redis.conf --port 6380
```

**3.1.2 （推荐此方法）**

* 创建多个redis目录，以端口号命名，比如：创建6379、6380两个目录，将redis的安装文件bin和conf拷贝至这两个目录。
* 修改6379目录下的redis.conf设置端口号为6379，修改6380目录下的redis.conf设置端口号为6380
* 启动6379和6380目录下的redis-server程序：

```
cd 6379

./redis-server . /redis.conf

cd 6380

./redis-server . /redis.conf
```

* 查询当前redis的进程：

```
ps aux|grep redis 
```

### **4.redis停止**

强行终止Redis进程可能会导致redis持久化数据丢失。正确停止Redis的方式应该是向Redis发送SHUTDOWN命令： 

```
cd /usr/local/redis

./bin/redis-cli shutdown
```

### **5.redis客户端**

在redis的安装目录中有redis的客户端，即redis-cli（Redis Command Line Interface），它是Redis自带的基于命令行的Redis客户端。

**5.1 连接redis服务端**

**5.1.1** 执行bin/redis-cli连接redis服务端：

​	![连接redis服务端](.\Redis\连接redis服务端.png)

**5.1.2** 指定连接redis服务的ip和端口：

```
./redis-cli -h 192.168.101.3 -p 6379 
```

* **注意：**redis-cli默认连接本机的redis，本机的redis没有启动则报拒绝连接的错误。

**5.2 向redis服务端发送命令**	

redis-cli连上redis服务后，可以在命令行发送命令。

* **ping：**Redis提供了`ping`命令来测试客户端与Redis的连接是否正常，如果连接正常会收到回复`PONG`

* **set/get：**使用set和get可以向redis设置数据、获取数据。

```
set name zhangsan

get name
```

* **del：**删除指定key的内容。

```
del name
```

* **Keys ：**查看当前库中所有的key值

### **6.redis多数据库**

**6.1 redis实例**

一个redis进程就是一个redis实例，一台服务器可以同时有多个redis实例，不同的redis实例提供不同的服务端口对外提供服务，每个redis实例之间互相没有影响。每个redis实例都包括自己的数据库，数据库中可以存储自己的数据。 

**6.2 多数据库测试**

一个Redis实例可以包括多个数据库，客户端可以指定连接某个redis实例的哪个数据库，就好比一个mysql中创建多个数据库，客户端连接时指定连接哪个数据库。

* 一个redis实例最多可提供16个数据库，下标从0到15，客户端默认连接第0号数据库，也可以通过select选择连接哪个数据库，如下连接1号库： 

```
select 1 
```

* 在1号库中查询上节设置的数据，结果查询不到：

```
get name 

//返回结果(nil)
```

* 重新选择第0号数据库，查询数据：

```
select 0

get name
```

* 如果选择一个不存在数据库则会报错：

```
select 16
//返回结果(error) 
ERR invaild DB index
```

* **注意：**redis不支持修改数据库的名称，只能通过select 0、select 1...选择数据库。

**6.3 注意问题**

在0号数据库存储数据，在1号数据库执行清空数据命令却把0号数据库的数据给清空了：

![两个数据库清除数据的问题](.\Redis\两个数据库清除数据的问题.png) 

**建议：不同的应用系统要使用不同的redis实例而不是使用同一个redis实例下的不同数据库。**

## 三、jedis

### **1.jedis介绍**

Redis不仅是使用命令来操作，现在基本上主流的语言都有客户端支持，比如java、C、C#、C++、php、Node.js、Go等。 在官方网站里列一些Java的客户端，有Jedis、Redisson、Jredis、JDBC-Redis、等其中官方推荐使用Jedis和Redisson， 在企业中用的最多的就是Jedis。Jedis同样也是托管在github上，[Jedis下载](https://github.com/xetorthio/jedis )

### **2.通过jedis连接redis单机**

**2.1 jar包**

commons-por2-2.3.jar，jedis-2.7.0.jar

```xml
//在pom.xml文件中引入

<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.7.0</version>
</dependency>
```

**2.2 单实例连接**

通过创建单实例jedis对象连接redis服务，如下代码：

```java
// 单实例连接redis
@Test
public void testJedisSingle() {
    Jedis jedis =  new Jedis("192.168.101.3", 6379);
    jedis.set("name", "bar");
    String name = jedis.get("name");
    System.out.println(name);
    jedis.close();
}
```

**2.3 连接超时解决**

由于linux防火墙默认开启，redis的服务端口6379并不在开放规则之内，所有需要将此端口开放访问或者关闭防火墙。

关闭防火墙命令：sevice iptables stop

如果是修改防火墙规则，可以修改：/etc/sysconfig/iptables文件

**2.4 使用连接池连接**

通过单实例连接redis不能对redis连接进行共享，可以使用连接池对redis连接进行共享，提高资源利用率，使用jedisPool连接redis服务，如下代码：

```
@Test
public void pool() {
	JedisPoolConfig config = new JedisPoolConfig();
	//最大连接数
	config.setMaxTotal(30);
	//最大连接空闲数
	config.setMaxIdle(2);
	JedisPool pool = new JedisPool(config, "192.168.101.3", 6379);
	Jedis jedis = null ;
	try {
        jedis = pool.getResource();
        jedis.set("name", "lisi");
        String name = jedis.get("name");
		System.out.println(name);
	}catch(Exception ex){
			ex.printStackTrace();
	}finally{
		if(jedis !=null){
		//关闭连接
		jedis.close();
		}
	}
}
```

 详细的连接池配置参数参考下节jedis和spring整合中applicationContext.xml的配置内容。

### **3.jedis与spring整合**

配置spring配置文件applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 				
       xmlns:mvc="http://www.springframework.org/schema/mvc"
  	   xmlns:context="http://www.springframework.org/schema/context"
	   xmlns:aop="http://www.springframework.org/schema/aop" 
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
       http://www.springframework.org/schema/beans/spring-beans-3.2.xsd 
       http://www.springframework.org/schema/mvc 
       http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd 
       http://www.springframework.org/schema/context 
       http://www.springframework.org/schema/context/spring-context-3.2.xsd 
       http://www.springframework.org/schema/aop 
       http://www.springframework.org/schema/aop/spring-aop-3.2.xsd 
       http://www.springframework.org/schema/tx 
       http://www.springframework.org/schema/tx/spring-tx-3.2.xsd ">
<!-- 连接池配置 -->
<bean id="jedisPoolConfig"  class="redis.clients.jedis.JedisPoolConfig" >
    <!-- 最大连接数 -->
    <property name="maxTotal"  value="30"  />
    <!-- 最大空闲连接数 -->
    <property name="maxIdle"  value="10"  />
    <!-- 每次释放连接的最大数目 -->
    <property name="numTestsPerEvictionRun" value="1024"/>
    <!-- 释放连接的扫描间隔（毫秒） -->
    <property name="timeBetweenEvictionRunsMillis" value="30000"  />
    <!-- 连接最小空闲时间 -->
    <property name="minEvictableIdleTimeMillis" value="1800000"  />
    <!-- 连接空闲多久后释放, 当空闲时间>该值 并且 空闲连接>最大空闲连接数时直接释放-->
    <property name="softMinEvictableIdleTimeMillis" value="10000"  />
    <!-- 获取连接时的最大等待毫秒数,小于零:阻塞不确定的时间,默认-1 -->
    <property name="maxWaitMillis" value="1500"  />
    <!-- 在获取连接的时候检查有效性, 默认false -->
    <property name="testOnBorrow" value="true"  />
    <!-- 在空闲时检查有效性, 默认false -->
    <property name="testWhileIdle" value="true"  />
    <!-- 连接耗尽时是否阻塞, false报异常,ture阻塞直到超时, 默认true -->
    <property name="blockWhenExhausted" value="false"  />
</bean>
<!-- redis单机 通过连接池 -->
<bean id="jedisPool" class="redis.clients.jedis.JedisPool" destroy-method=" close">
    <constructor-arg name="poolConfig" ref="jedisPoolConfig"/>
    <constructor-arg name="host" value="192.168.25.145"/>
    <constructor-arg name="port" value="6379"/>
</bean>
</beans>
```

测试代码：

```java
private ApplicationContext applicationContext;

@Before
public void init() {
	applicationContext = new ClassPathXmlApplicationContext(
			"classpath:applicationContext.xml");
}

@Test
public void testJedisPool() {
    JedisPool pool = (JedisPool) applicationContext.getBean("jedisPool");
    try  {
        Jedis jedis = pool.getResource();
        jedis.set("name", "lisi");
        String name = jedis.get("name");
        System.out.println(name);
    }catch(Exception ex){
        ex.printStackTrace();
    }finally{
        if(jedis != null){
            //关闭连接
            jedis.close();
        }
    }
}
```
## 四、数据类型--string

### **1.redis string介绍**

Redis中没有使用C语言的字符串表示，而是自定义一个数据结构叫**SDS（simple dynamic string）即简单动态字符串**。

打开下载的redis源码包，找到src下的sds.h文件查看sds源码：

```
struct sdshdr {
    //字符串长度
	unsigned int len;
	//buf数组中未使用的字节数量
    unsigned int free;
	//用于保存字符串
    char buf[];
};
```

c语言对字符串的存储是使用字符数组，遇到'\0'字符则认为字符串结束，redis的字符串可以存储任何类型的数据，因为任何类型数据都可以表示成二进制，sds结构中的char buf[]就是存储了二进制数据。

> redis的字符串是二进制安全的，什么是二进制安全？简单理解就是存入什么数据取出的还是什么数据。redis中的sds不像c语言处理字符串那样遇到'\0'字符则认证字符串结束，它不会对存储进去的二进制数据进行处理，存入什么数据取出还是什么数据。

### **2.命令** 

**2.1 赋值：**SET key value的形式

**2.2 取值：**GET key

当键不存在时返回空结果。

**GETSET key value：**取值时同时对key进行赋值操作。

**2.3 删除：**Del key

**2.4 数值增减：**

* **递增数字：** INCR key，当存储的**字符串是整数**时，Redis提供了一个实用的命令INCR，其作用是让当前键值递增，并返回递增后的值。 

* **增加指定的整数：**INCRBY key increment，增加指定key的整数，并且设定每次增加的步长。

* **递减数值：**DECR key

### **3.其它命令(自学)**

**3.1 减少指定的整数：**DECRBY key decrement，减少指定的整数，并指定减少的步长。

```
127.0.0.1:6379> decr num

(integer) 6

127.0.0.1:6379> decr num

(integer) 5

127.0.0.1:6379> decrby num 3

(integer) 2

127.0.0.1:6379> decrby num 3

(integer) -1 
```

**3.2 向尾部追加值：** APPEND key value，APPEND的作用是向键值的末尾追加value。如果键不存在则将该键的值设置为value，即相当于 SET key value。返回值是追加后字符串的总长度。 

```
127.0.0.1:6379> set str hello

OK

127.0.0.1:6379> append str " world!"

(integer) 12

127.0.0.1:6379> get str 

"hello world!"
```

**3.3  获取字符串长度：**STRLEN key，STRLEN命令返回键值的长度，如果键不存在则返回0。 

```
127.0.0.1:6379> strlen str 
(integer) 0
127.0.0.1:6379> set str hello
OK
127.0.0.1:6379> strlen str 
(integer) 5
```

 **3.4 同时设置/获取多个键值：**MSET key value [key value …]，MGET key [key …]

```
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3

OK

127.0.0.1:6379> get k1

"v1"

127.0.0.1:6379> mget k1 k3

1) "v1"

2) "v3"
```

### **4.应用**

**4.1 自增主键**

商品编号、订单号采用string的递增数字特性生成。

```
//定义商品编号的key：items:id

192.168.101.3:7003> INCR items:id

(integer) 2

192.168.101.3:7003> INCR items:id

(integer) 3
```

java代码参考测试工程。

## 五、数据类型--hash

### **1.使用string的问题**

假设有User对象以JSON序列化的形式存储到Redis中，User对象有id，username、password、age、name等属性，存储的过程如下： User对象-》json(string)-》redis 。如果在业务上只是更新age属性，其他的属性并不做更新我应该怎么做呢？ 如果仍然采用上边的方法在传输、处理时会造成资源浪费，下边讲的hash可以很好的解决这个问题。

### **2.redis hash介绍**

hash叫散列类型，它提供了字段和字段值的映射。字段值只能是字符串类型，不支持散列类型、集合类型等其它类型。如下：

![redis hash介绍](.\Redis\redis hash介绍.png) 

 

思考：redis hash存储比关系数据库的好处？

### **3.命令**

**3.1 赋值：**HSET key field value，一次只能设置一个字段值；HMSET key field value [field value ...]，一次可以设置多个字段值

```
127.0.0.1:6379> hset user username zhangsan

(integer) 1

127.0.0.1:6379> hmset user age 20 username lisi

OK
```

**3.2 取值：** HGET key field，一次只能获取一个字段值；HMGET key field [field ...]，一次可以获取多个字段值；HGETALL key，获取所有字段的值。

```
127.0.0.1:6379> hget user username

"zhangsan“

127.0.0.1:6379> hmget user age username

1) "20"

2) "lisi"

127.0.0.1:6379> hgetall user

1) "age"

2) "20"

3) "username"

4) "lisi"
```

* **注意：**HSET命令不区分插入和更新操作，当执行插入操作时HSET命令返回1，当执行更新操作时返回0.

**3.3 删除字段：**HDEL key field [field ...]，可以删除一个或多个字段，返回值是被删除的字段个数 

```
127.0.0.1:6379> hdel user age

(integer) 1

127.0.0.1:6379> hdel user age name

(integer) 0

127.0.0.1:6379> hdel user age username

(integer) 1 
```

**3.4 增加数字：** HINCRBY key field increment

```
127.0.0.1:6379> hincrby user age 2	将用户的年龄加2

(integer) 22

127.0.0.1:6379> hget user age		获取用户的年龄

"22“
```

### **4.其它命令(自学)**

**4.1 判断字段是否存在：**HEXISTS key field；HSETNX key field value，当字段不存在时赋值，类似HSET，区别在于如果字段已经存在，该命令不执行任何操作。

```
127.0.0.1:6379> hexists user age	

(integer) 1

127.0.0.1:6379> hexists user name

(integer) 0

127.0.0.1:6379> hsetnx user age 30	

(integer) 0
```

**4.2 只获取字段名或字段值：**HKEYS key，HVALS key

```
127.0.0.1:6379> hmset user age 20 name lisi 

OK

127.0.0.1:6379> hkeys user

1) "age"

2) "name"

127.0.0.1:6379> hvals user

1) "20"

2) "lisi"
```

**4.3 获取字段数量：**HLEN key

```
127.0.0.1:6379> hlen user

(integer) 2
```

### **5.应用**

**5.1 商品信息**
商品id、商品名称、商品描述、商品库存、商品好评

```
定义商品1001的信息在 redis中的key为：items:1001

//存储商品信息
192.168.101.3:7003> HMSET items:1001 id 3 name apple price 999.9

OK

//获取商品信息
192.168.101.3:7003> HGET items:1001 id

"3"

192.168.101.3:7003> HGETALL items:1001

1) "id"

2) "3"

3) "name"

4) "apple"

5) "price"

6) "999.9"
```

java代码参考测试工程。

## 六、数据类型--list

### **1.ArrayList与LinkedList的区别**

ArrayList使用数组方式存储数据，所以根据索引查询数据速度快，而新增或者删除元素时需要设计到位移操作，所以比较慢；LinkedList使用双向链接方式存储数据，每个元素都记录前后元素的指针，所以插入、删除数据时只是更改前后元素的指针指向即可，速度非常快，然后通过下标查询元素时需要从头开始索引，所以比较慢，但是如果查询前几个元素或后几个元素速度比较快。

![ArrayList与LinkedList的区别](.\Redis\ArrayList与LinkedList的区别.png) 

![ArrayList与LinkedList的区别2](.\Redis\ArrayList与LinkedList的区别2.png) 

### **2.redis list介绍**

列表类型（list）可以存储一个有序的字符串列表，常用的操作是向列表两端添加元素，或者获得列表的某一个片段。列表类型内部是使用双向链表（double linked list）实现的，所以向列表两端添加元素的时间复杂度为0(1)，获取越接近两端的元素速度就越快。这意味着即使是一个有几千万个元素的列表，获取头部或尾部的10条记录也是极快的。

### **3.命令**

**3.1 向列表两端增加元素：** LPUSH key value [value ...]，RPUSH key value [value ...]

```
//向列表左边增加元素

127.0.0.1:6379> lpush list1 1 2 3

(integer) 3

//向列表右边增加元素

127.0.0.1:6379> rpush list1 4 5 6

(integer) 3
```

**3.2 查看列表：**LRANGE key start stop，LRANGE命令是列表类型最常用的命令之一，获取列表中的某一片段，将返回start、stop之间的所有元素（包含两端的元素），索引从0开始。索引可以是负数，如：“-1”代表最后边的一个元素。 

```
127.0.0.1:6379> lrange list1 0 2

 1) "2" 

 2) "1" 

 3) "4"
```

**3.3 从列表两端弹出元素：** LPOP key，RPOP key，LPOP命令从列表左边弹出一个元素，会分两步完成，第一步是将列表左边的元素从列表中移除，第二步是返回被移除的元素值。 

```
127.0.0.1:6379> lpop list1

"3“

127.0.0.1:6379> rpop list1

"6“
```

**3.4 获取列表中元素的个数：** LLEN key

```
127.0.0.1:6379> llen list1

(integer) 2
```

### **4. 其它命令(自学)**

**4.1 删除列表中指定的值：**LREM key count value，LREM命令会删除列表中前count个值为value的元素，返回实际删除的元素个数。根据count值的不同，该命令的执行方式会有所不同： 

* 当count>0时， LREM会从列表左边开始删除。 

* 当count<0时， LREM会从列表右边开始删除。 

* 当count=0时， LREM删除所有值为value的元素。 

**4.2 获得/设置指定索引的元素值：** LINDEX key index，LSET key index value

```
127.0.0.1:6379> lindex llist 2

"1"

127.0.0.1:6379> lset llist 2 2

OK

127.0.0.1:6379> lrange llist 0 -1

1) "6"

2) "5"

3) "2"

4) "2"
```

 **4.3 只保留列表指定片段，指定范围和LRANGE一致：**LTRIM key start stop

```
127.0.0.1:6379> lrange llist 0 -1

1) "6"

2) "5"

3) "0"

4) "2"

127.0.0.1:6379> ltrim llist 0 2

OK

127.0.0.1:6379> lrange llist 0 -1

1) "6"

2) "5"

3) "0"
```

**4.4 向列表中插入元素：** LINSERT key BEFORE|AFTER pivot value，该命令首先会在列表中从左到右查找值为pivot的元素，然后根据第二个参数是BEFORE还是AFTER来决定将value插入到该元素的前面还是后面。 

```
127.0.0.1:6379> lrange list 0 -1

1) "3"

2) "2"

3) "1"

127.0.0.1:6379> linsert list after 3 4

(integer) 4

127.0.0.1:6379> lrange list 0 -1

1) "3"

2) "4"

3) "2"

4) "1"
```

**4.5 将元素从一个列表转移到另一个列表中：** RPOPLPUSH source destination

```
127.0.0.1:6379> rpoplpush list newlist

"1"

127.0.0.1:6379> lrange newlist 0 -1

1) "1"

127.0.0.1:6379> lrange list 0 -1

1) "3"

2) "4"

3) "2"
```

### **5.应用**

**5.1 商品评论列表**

在redis中创建商品评论列表，用户发布商品评论，将评论信息转成json存储到list中。用户在页面查询评论列表，从redis中取出json数据展示到页面。

```
//商品编号为1001的商品评论key：items: comment:1001

192.168.101.3:7001> LPUSH items:comment:1001 '{"id":1,"name":"商品不错，很好！！","date":1430295077289}'
```

java代码参考测试工程。

## 七、数据类型--set

### **1.redis set介绍**

在集合中的每个元素都是不同的，且没有顺序。

 集合类型和列表类型的对比：

![集合类型和列表类型](.\Redis\集合类型和列表类型.png) 

集合类型的常用操作是向集合中加入或删除元素、判断某个元素是否存在等，由于集合类型的Redis内部是使用值为空的散列表实现，所有这些操作的时间复杂度都为0(1)。 Redis还提供了多个集合之间的交集、并集、差集的运算。

### **2.命令**

**2.1 增加/删除元素：**SADD key member [member ...]，SREM key member [member ...]

```
127.0.0.1:6379> sadd set a b c

(integer) 3

127.0.0.1:6379> sadd set a

(integer) 0

127.0.0.1:6379> srem set c d

(integer) 1
```

**2.2 获得集合中的所有元素：** SMEMBERS key

```
127.0.0.1:6379> smembers set

1) "b"

2) "a”
```

**2.3 判断元素是否在集合中：**SISMEMBER key member，无论集合中有多少元素都可以极速的返回结果。 

```
127.0.0.1:6379> sismember set a

(integer) 1

127.0.0.1:6379> sismember set h

(integer) 0
```

### **3.其它命令**

**3.1 集合的差集运算：**A-B，属于A并且不属于B的元素构成的集合。 

```
SDIFF key [key ...]

127.0.0.1:6379> sadd setA 1 2 3

(integer) 3

127.0.0.1:6379> sadd setB 2 3 4

(integer) 3

127.0.0.1:6379> sdiff setA setB 

1) "1"

127.0.0.1:6379> sdiff setB setA 

1) "4"
```

**3.2 集合的交集运算：** A ∩ B，属于A且属于B的元素构成的集合。 

```
127.0.0.1:6379> sinter setA setB 

1) "2"

2) "3"
```

**3.3 集合的并集运算：**A **∪** B，属于A或者属于B的元素构成的集合

```
127.0.0.1:6379> sunion setA setB

1) "1"

2) "2"

3) "3"

4) "4"
```

### **4.其它命令(自学)**

**4.1 获得集合中元素的个数：**SCARD key

```
127.0.0.1:6379> smembers setA 

1) "1"

2) "2"

3) "3"

127.0.0.1:6379> scard setA 

(integer) 3
```

**4.2 从集合中弹出一个元素：**SPOP key

```
127.0.0.1:6379> spop setA

"1“
```

* **注意：**由于集合是无序的，所有SPOP命令会从集合中随机选择一个元素弹出 

## 八、数据类型--sorted set

### **1.redis sorted set介绍**

在集合类型的基础上有序集合类型为集合中的每个元素都关联一个分数，这使得我们不仅可以完成插入、删除和判断元素是否存在在集合中，还能够获得分数最高或最低的前N个元素、获取指定分数范围内的元素等与分数有关的操作。 

* 在某些方面有序集合和列表类型有些相似。 
  * 二者都是有序的（怎么存进去顺序就是什么样子的）。 
  * 二者都可以获得某一范围的元素。 

* 二者有着很大区别： 
  * 列表类型是通过链表实现的，获取靠近两端的数据速度极快，而当元素增多后，访问中间数据的速度会变慢。 
  * 有序集合类型使用散列表实现，所有即使读取位于中间部分的数据也很快。 
  * 列表中不能简单的调整某个元素的位置，但是有序集合可以（通过更改分数实现） 
  * 有序集合要比列表类型更耗内存。 

### **2.命令**

**2.1 增加元素：**ZADD key score member [score member ...]，向有序集合中加入一个元素和该元素的分数，如果该元素已经存在则会用新的分数替换原有的分数。返回值是新加入到集合中的元素个数，不包含之前已经存在的元素。 

```
127.0.0.1:6379> zadd scoreboard 80 zhangsan 89 lisi 94 wangwu

(integer) 3

127.0.0.1:6379> zadd scoreboard 97 lisi

(integer) 0
```

 **2.2 获取元素的分数：**ZSCORE key member

```
127.0.0.1:6379> zscore scoreboard lisi 

"97"
```

**2.3 删除元素：**ZREM key member [member ...]，移除有序集key中的一个或多个成员，不存在的成员将被忽略。当key存在但不是有序集类型时，返回一个错误。

```
127.0.0.1:6379> zrem scoreboard lisi

(integer) 1
```

**2.4 获得排名在某个范围的元素列表：**ZRANGE key start stop [WITHSCORES]，照元素分数从小到大的顺序返回索引从start到stop之间的所有元素（包含两端的元素）；ZREVRANGE key start stop [WITHSCORES]，照元素分数从大到小的顺序返回索引从start到stop之间的所有元素（包含两端的元素）

```
127.0.0.1:6379> zrange scoreboard 0 2

1) "zhangsan"

2) "wangwu"

3) "lisi“

127.0.0.1:6379> zrevrange scoreboard 0 2

1) " lisi "

2) "wangwu"

3) " zhangsan “
```

 如果需要获得元素的分数的可以在命令尾部加上`WITHSCORES`参数*

```
127.0.0.1:6379> zrange scoreboard 0 1 WITHSCORES

1) "zhangsan"

2) "80"

3) "wangwu"

4) "94"
```

### **3. 其它命令(自学)**

**3.1 获得指定分数范围的元素：** ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]

```
127.0.0.1:6379> ZRANGEBYSCORE scoreboard 90 97 WITHSCORES*

1) "wangwu"

2) "94"

3) "lisi"

4) "97"

127.0.0.1:6379> ZRANGEBYSCORE scoreboard 70 100 limit 1 2*

1) "wangwu"

2) "lisi"
```

**3.2 增加某个元素的分数：**ZINCRBY  key increment member，返回值是更改后的分数。 

 

n 增加某个元素的分数，返回值是更改后的分数。 

```
//给lisi加4分 

127.0.0.1:6379> ZINCRBY scoreboard  4 lisi 

"101“
```

**3.3 获得集合中元素的数量：** ZCARD key

 

n 获得集合中元素的数量 

```
127.0.0.1:6379> ZCARD scoreboard

(integer) 3
```

**3.4 获得指定分数范围内的元素个数：** ZCOUNT key min max

```
127.0.0.1:6379> ZCOUNT scoreboard 80 90*

(integer) 1
```

**3.5 按照排名范围删除元素：**ZREMRANGEBYRANK key start stop

n 按照排名范围删除元素 

```
127.0.0.1:6379> ZREMRANGEBYRANK scoreboard 0 1

(integer) 2

127.0.0.1:6379> ZRANGE scoreboard 0 -1

1) "lisi"
```

**3.6 按照分数范围删除元素：**ZREMRANGEBYSCORE key min max

```
127.0.0.1:6379> zadd scoreboard 84 zhangsan	

(integer) 1

127.0.0.1:6379> ZREMRANGEBYSCORE scoreboard 80 100

(integer) 1
```

3.7 获取元素的排名：ZRANK key member，ZREVRANK key member

```
//从小到大 

127.0.0.1:6379> ZRANK scoreboard lisi 

(integer) 0

//从大到小 

127.0.0.1:6379> ZREVRANK scoreboard zhangsan 

(integer) 1
```

### **4.应用**

**4.1 商品销售排行榜**

根据商品销售量对商品进行排行显示，定义sorted set集合，商品销售量为元素的分数。

```
//定义商品销售排行榜key：items:sellsort

//写入商品销售量：商品编号1001的销量是9，商品编号1002的销量是10

192.168.101.3:7007> ZADD items:sellsort 9 1001 10 1002

//商品编号1001的销量加1

192.168.101.3:7001> ZINCRBY items:sellsort 1 1001

//商品销量前10名：

192.168.101.3:7001> ZRANGE items:sellsort 0 9 withscores
```

## 九、命令

### **1.设置key的生存时间**

Redis在实际使用过程中更多的用作缓存，然而缓存的数据一般都是需要设置生存时间的，即：到期后数据销毁。 

| 命令                     | 说明                                                 |
| ------------------------ | ---------------------------------------------------- |
| EXPIRE key seconds       | 设置key的生存时间（单位：秒）key在多少秒后会自动删除 |
| TTL key                  | 查看key生于的生存时间                                |
| PERSIST key              | 清除生存时间                                         |
| PEXPIRE key milliseconds | 生存时间设置单位为：毫秒                             |

```
//设置test的值为1
192.168.101.3:7002> set test 1

OK
//获取test的值
192.168.101.3:7002> get test		

"1"
//设置test的生存时间为5秒
192.168.101.3:7002> EXPIRE test 5

(integer) 1
//查看test的生于生成时间还有1秒删除
192.168.101.3:7002> TTL test			

(integer) 1

192.168.101.3:7002> TTL test

(integer) -2
//获取test的值，已经删除
192.168.101.3:7002> get test		

(nil)
```

### **2.其它命令（自学）**

**2.1 keys：**返回满足给定pattern 的所有key

```
redis 127.0.0.1:6379> keys mylist

1) "mylist"

2) "mylist5"

3) "mylist6"

4) "mylist7"

5) "mylist8"
```

**2.2 exists：**确认一个key 是否存在

n exists

```
redis 127.0.0.1:6379> exists HongWan

(integer) 0

redis 127.0.0.1:6379> exists age

(integer) 1

redis 127.0.0.1:6379>
```

**2.3 del：**删除一个key

```
redis 127.0.0.1:6379> del age

(integer) 1

redis 127.0.0.1:6379> exists age

(integer) 0

redis 127.0.0.1:6379>
```

**2.4 rename：**重命名key

```
redis 127.0.0.1:6379[1]> keys 

1) "age"

redis 127.0.0.1:6379[1]> rename age age_new

OK

redis 127.0.0.1:6379[1]> keys

1) "age_new"

redis 127.0.0.1:6379[1]>

//age 成功的被我们改名为age_new 了
```

**2.5 type：**返回值的类型

```
redis 127.0.0.1:6379> type addr

string

redis 127.0.0.1:6379> type myzset2

zset

redis 127.0.0.1:6379> type mylist

list

redis 127.0.0.1:6379>

//这个方法可以非常简单的判断出值的类型
```

### **3.服务器命令(自学)**

**3.1 ping：**测试连接是否存活

```
redis 127.0.0.1:6379> ping

PONG

//执行下面命令之前，我们停止redis 服务器进行测试

redis 127.0.0.1:6379> ping

Could not connect to Redis at 127.0.0.1:6379: Connection refused

//执行下面命令之前，我们启动redis 服务器

not connected> ping

PONG

redis 127.0.0.1:6379>
```

**3.2 echo：**在命令行打印一些内容

```
redis 127.0.0.1:6379> echo HongWan

"HongWan"

redis 127.0.0.1:6379>
```

**3.3 select：**选择数据库。Redis 数据库编号从0~15，我们可以选择任意一个数据库来进行数据的存取。

```
redis 127.0.0.1:6379> select 1

OK

redis 127.0.0.1:6379[1]> select 16

(error) ERR invalid DB index

redis 127.0.0.1:6379[16]>

//当选择16 时，报错，说明没有编号为16 的这个数据库
```

**3.4 quit：**退出连接。

```
redis 127.0.0.1:6379> quit 
```

**3.5 dbsize：**返回当前数据库中key 的数目。

```
redis 127.0.0.1:6379> dbsize

(integer) 18

redis 127.0.0.1:6379>

//结果说明此库中有18 个key
```

**3.6 info：**获取服务器的信息和统计。

```
redis 127.0.0.1:6379> info

redis_version:2.2.12

redis_git_sha1:00000000

redis_git_dirty:0

arch_bits:32

multiplexing_api:epoll

process_id:28480

uptime_in_seconds:2515

uptime_in_days:0

......
```

**3.7 flushdb：**删除当前选择数据库中的所有key。

```
redis 127.0.0.1:6379> dbsize

(integer) 18

redis 127.0.0.1:6379> flushdb

OK

redis 127.0.0.1:6379> dbsize

(integer) 0

redis 127.0.0.1:6379>

//在本例中我们将0 号数据库中的key 都清除了。
```

**3.8 flushall：**删除所有数据库中的所有key。

```
redis 127.0.0.1:6379[1]> dbsize

(integer) 1

redis 127.0.0.1:6379[1]> select 0

OK

redis 127.0.0.1:6379> flushall

OK

redis 127.0.0.1:6379> select 1

OK

redis 127.0.0.1:6379[1]> dbsize

(integer) 0

redis 127.0.0.1:6379[1]>

//在本例中我们先查看了一个1 号数据库中有一个key，然后我切换到0 号库执行flushall 命令，结果1 号库中的key 也被清除了，说是此命令工作正常。
```

## 十、持久化(了解)

Redis的高性能是由于其将所有数据都存储在了内存中，为了使Redis在重启之后仍能保证数据不丢失，需要将数据从内存中同步到硬盘中，这一过程就是持久化。

Redis支持两种方式的持久化，一种是RDB方式，一种是AOF方式。可以单独使用其中一种或将二者结合使用。

### **1.RDB持久化**

RDB方式的持久化是通过快照（snapshotting）完成的，当符合一定条件时Redis会自动将内存中的数据进行快照并持久化到硬盘。RDB是Redis默认采用的持久化方式，在redis.conf配置文件中默认有此下配置：

```
save 900 1

save 300 10

save 60 10000
```

save 开头的一行就是持久化配置，可以配置多个条件（每行配置一个条件），每个条件之间是“或”的关系，“save 900 1”表示15分钟（900秒钟）内至少1个键被更改则进行快照，“save 300 10”表示5分钟（300秒）内至少10个键被更改则进行快照。

**在redis.conf中：**

* 配置dir指定rdb快照文件的位置

* 配置dbfilenam指定rdb快照文件的名称

 Redis启动后会读取RDB快照文件，将数据从硬盘载入到内存。根据数据量大小与结构和服务器性能不同，这个时间也不同。通常将记录一千万个字符串类型键、大小为1GB的快照文件载入到内存中需要花费20～30秒钟。

 **问题总结：**

通过RDB方式实现持久化，一旦Redis异常退出，就会丢失最后一次快照以后更改的所有数据。这就需要开发者根据具体的应用场合，通过组合设置自动快照条件的方式来将可能发生的数据损失控制在能够接受的范围。如果数据很重要以至于无法承受任何损失，则可以考虑使用AOF方式进行持久化。

**1.1 配置过程**

* 进入redis的bin目录下，可以看见自动生成了dump.rdb文件
* 进入cof目录下的redis.conf文件，配置dir指定rdb快照文件的位置，配置dbfilenam指定rdb快照文件的名称，配置save来自定义持久化方式。

### **2.AOF持久化**

默认情况下Redis没有开启AOF（append only file）方式的持久化，可以通过appendonly参数开启：

```
appendonly yes
```

开启AOF持久化后每执行一条会更改Redis中的数据的命令，Redis就会将该命令写入硬盘中的AOF文件。AOF文件的保存位置和RDB文件的位置相同，都是通过dir参数设置的，默认的文件名是appendonly.aof，可以通过appendfilename参数修改：

```
appendfilename appendonly.aof
```

具体步骤：

```
//在bin目录下

./redis-cli shutdown

ls

vi redis.conf
//进入配置文件中,修改
appendonly yes

./redis-server redis.conf

ps -aux|grep redis
```

### **3.RDB和AOF的区别**

**3.1 rdb**

可以设置间隔多长时间保存一次（Redis不用任何配置默认的持久化方案）

* 优点：让redis的数据存取速度变快。

* 缺点：服务器断电时会丢失部分数据（数据的完整性得不到保证）。

**3.2 aof**

可以设置实时保存

* 优点：持久化良好,能保证数据的完整性
* 缺点：大大降低了redis系统的存取速度

## 十一、主从复制（了解）

### **1.什么是主从复制**

 持久化保证了即使redis服务重启也不会丢失数据，因为redis服务重启后会将硬盘上持久化的数据恢复到内存中，但是当redis服务器的硬盘损坏了可能会导致数据丢失，如果通过redis的主从复制机制就可以避免这种单点故障，如下图：

![什么是主从复制](F:\学习笔记\Redis\什么是主从复制.png)

说明：

* 主redis中的数据有两个副本（replication）即从redis1和从redis2，即使一台redis服务器宕机其它两台redis服务也可以继续提供服务。

* 主redis中的数据和从redis上的数据保持实时同步，当主redis写入数据时通过主从复制机制会复制到两个从redis服务上。

* 只有一个主redis，可以有多个从redis。

* 主从复制不会阻塞master，在同步数据时，master 可以继续处理client 请求

* 一个redis可以即是主又是从，如下图：

![主从关系](.\Redis\主从关系.png)

[三张图秒懂Redis集群设计原理](https://blog.csdn.net/yejingtao703/article/details/78484151)

### **2.主从配置**

**2.1 主redis配置**

无需特殊配置。

**2.2 从redis配置**

修改从redis服务器上的redis.conf文件，添加**slaveof 主redis ip  主redis端口**

```
# slaveof <masterip> <masterport>
slaveof 192.168.101.3 6379

//上边的配置说明当前该从redis服务器所对应的主redis是192.168.101.3，端口是6379
```

### **3.主从复制过程**

**3.1 完整复制**

在redis2.8版本之前主从复制过程如下图：

![完整复制](F:\学习笔记\Redis\完整复制.png)

复制过程说明：

* slave 服务启动，slave 会建立和master 的连接，发送sync 命令。

* master启动一个后台进程将数据库快照保存到RDB文件中
  * **注意：**此时如果生成RDB文件过程中存在写数据操作会导致RDB文件和当前主redis数据不一致，所以此时master 主进程会开始收集写命令并缓存起来。

* master 就发送RDB文件给slave

* slave 将文件保存到磁盘上，然后加载到内存恢复

* master把缓存的命令转发给slave
  * **注意：**后续master 收到的写命令都会通过开始建立的连接发送给slave。当master 和slave 的连接断开时slave 可以自动重新建立连接。如果master 同时收到多个slave 发来的同步连接命令，只会启动一个进程来写数据库镜像，然后发送给所有slave。

完整复制的问题：

在redis2.8之前从redis每次同步都会从主redis中复制全部的数据，如果从redis是新创建的从主redis中复制全部的数据这是没有问题的，但是，如果当从redis停止运行，再启动时可能只有少部分数据和主redis不同步，此时启动redis仍然会从主redis复制全部数据，这样的性能肯定没有只复制那一小部分不同步的数据高。

**3.2 部分复制**

![部分复制](F:\学习笔记\Redis\部分复制.png)

部分复制说明：

从机连接主机后，会主动发起 PSYNC 命令，从机会提供 master的runid(机器标识，随机生成的一个串) 和 offset（数据偏移量，如果offset主从不一致则说明数据不同步），主机验证 runid 和 offset 是否有效， runid 相当于主机身份验证码，用来验证从机上一次连接的主机，如果runid验证未通过则，则进行全同步，如果验证通过则说明曾经同步过，根据offset同步部分数据。

## 十二、其他相关扩展学习

### **1. redis的事务**

[Redis - 事务（multi，exec，watch，unwatch）](https://blog.csdn.net/wgh1015398431/article/details/53156027)

### **2. redis的订阅发布**

[15天玩转redis —— 第九篇 发布/订阅模式](https://www.cnblogs.com/huangxincheng/p/5002794.html)

[edis的发布、订阅模式](https://blog.csdn.net/xichengqc/article/details/88421299)

### **3. 哨兵模式**

[Redis哨兵（Sentinel）模式](https://www.jianshu.com/p/06ab9daf921d)

[Redis哨兵模式（sentinel）学习总结及部署记录（主从复制、读写分离、主从切换）](https://www.cnblogs.com/kevingrace/p/9004460.html)

### **4. 缓存和定时清除的问题**

[redis相关原理及面试官由浅到深必问的15大问题](https://blog.csdn.net/zlc3323/article/details/80836881)

[为什么分布式一定要有redis，redis的一些优缺点](https://blog.csdn.net/hcmony/article/details/80694560)

[Redis系列十：缓存雪崩、缓存穿透、缓存预热、缓存更新、缓存降级](https://www.cnblogs.com/leeSmall/p/8594542.html)  

> 个人理解：
>
> redis 一般配合传统的数据库使用，所以就有以上缓存的问题，如果单纯只是自己直接使用redis，查不到就是查不到，不会有上面的问题。 

### 5. pipeline流水线原理解析

正常的情况下，客户端发送一个命令请求，等待服务器端回复一个响应，有多少个命令，就会有多少个连接并响应，虽然redis处理命令很快，但是大部分时间都用在请求和响应这个往返时间了。

pipeline是一种通信协议，需要在java中看到效果。使用pipeline技术，可以不用等待服务器响应，一直发送请求，服务器再进行响应。

> 这个一直发送请求的过程是相对客户端来说，例如一万条命令请求，java只知道一直发送命令，但是这些命令只是存储在一个buffer里，并没有真正的发送到服务器，等到buffer有一定大小（8k）的时候才会进行请求，所以最后需要使用sync()方法或close()将管道内最后的命令flush掉，否则会有一些命令会留在buffer中没有发送。而服务器端也不是一直进行响应，而是将维护一个队列，将响应存储在队列中，最后进行全部响应给客户端。

注意几个要点：

* 发送的请求命令不要太多，有两个问题：服务器响应的时候会维护队列，如果过长的话，存储队列也是需要内存的；如果使用syncAndReturnAll()，并用一个list集合存储，会导致list存储的大小超出规定大小。

* 要使用sync或syncAndReturnAll或close方法，将buffer中命令flush。









## 参考：

[随笔分类 - redis](https://www.cnblogs.com/leeSmall/category/1090974.html)

