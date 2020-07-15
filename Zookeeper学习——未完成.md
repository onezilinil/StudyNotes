# Zookeeper学习

## 一、Zookeeper介绍

### 1. Zookeeper简介

Zookeeper就是用来解决分布式协作中的难点的。Zookeeper是google的chubby项目开源实现，最早是hadoop的子项目。

### 2. 分布式介绍

#### 2.1 分布式的特点

分布式主要是下面两个方面：

* 任务拆分：任务拆分指的是把传统的单节点服务拆分成多个节点服务部署到不同的机器上对外提供服务。比如一个传统服务有订单+支付+物流等3个模块，拆分成订单系统、支付系统、物流系统3个服务。
* 节点分工：如上面的服务拆分后，订单系统、支付系统、物流系统各司其职。

#### 2.2 分布式和集群的区别

* 分布式
  * 解决高可用，高并发的。
  * 分布式一种工作方式，例如：一个工作任务需要10个小时（单节点），分布式：10台机器，任务只需要1个小时就能够完成。

* 集群
  * 解决的是高可用。
  * 物理上来定义，例如：一个工作任务需要10个小时（单节点），10台机器，任务还是10个小时。

#### 2.3 分布式协作中的难点

* 保证节点高可用（节点故障）
* 数据的一致性
* 通讯异常
* 网络分区
  .......

### 3. Zookeeper的使用场景

* 小米米聊、淘宝Taokeeper其实是类zookeeper。
* Kafka使用zookeeper。消息发布订阅，其中zk就是用于检测节点崩溃。实现主题的发现，并且保持主题的生产和消费状态。
* Hbase三段查找，Root-Region=》Meta Region=》Region（Table）。hbase的元数据信息放在HBase。HMaster挂掉，马上要节点恢复。
* Hadoop。NameNode（SecondaryNameNode），HA Hadoop。一般情况下一个简单的hadoop集群，只有一个NameNode，如果NameNode挂掉，hadoop集群不可用。HA Haoop里面就要用到zk。

### 4. Zookeeper解决哪些问题

* Master节点管理
  * 解决的问题：Master高可用（挂掉以后，谁来负责工作），保证唯一。

* 配置文件管理
  * 解决的问题：统一把配置文件存放zk，由ZK统一分发修改的内容到各台机器。

* 发布与订阅
  * 解决的问题：发布者(producer)将数据发布到zk节点上，供订阅者(consumer)动态获取。

* 分布式锁
  * 分布式环境访问同一个资源，由第三方配锁实现。zk统一进行协调，保证数据的一致性。

* 集群的管理
  * Worker集群监控，保证主数据和备份数据的一致

## 二、Zookeeper安装配置

环境准备：一台安装有jdk的虚拟机：192.168.152.130

### 1. 安装

#### 1.1 下载

```
cd /software wget http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.4.6/zookeeper-3.4.6.tar.gz
```

### 1.2.解压

```
tar -zxvf zookeeper-3.4.6.tar.gz
```

## 2. 配置

**说明：这里配置的是伪分布式的zookeeper**

**注意：**配置之前一定要在/etc/hosts里面配置主机映射,否则会报错误：

ERROR [master:3890:QuorumCnxManager$Listener@517] - Exception while listening
java.net.SocketException: Unresolved address

```
vim /etc/hosts
```

![img](https://images2018.cnblogs.com/blog/1227483/201808/1227483-20180831204603462-2003914940.png)

### 2.1 先建立zookeeper的三个数据目录

```
mkdir -p /zookeeper/zk1
mkdir -p /zookeeper/zk2
mkdir -p /zookeeper/zk3
```

![img](https://images2018.cnblogs.com/blog/1227483/201808/1227483-20180831003630433-964736593.png)

### 2.2 查看zookeeper的配置文件（已删掉多余的配置和注释）

```
vim zoo_sample.cfg
tickTime=2000   #session的会话时间 以ms为单位
initLimit=10      #服务器启动以后，master和slave通讯的时间
syncLimit=5       #master和slave之间的心跳检测时间，检测slave是否存活
dataDir=/tmp/zookeeper  #保存zk的快照和数据
clientPort=2181      #客户端访问zk的端口
```

### 2.3 复制zoo_sample.cfg为zoo1.cfg添加如下配置

```
cp zoo_sample.cfg zoo1.cfgvim zoo1.cfg
```

配置：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
#session的会话时间 以ms为单位
tickTime=2000

#服务器启动以后，master和slave通讯的时间
initLimit=10

#master和slave之间的心跳检测时间，检测slave是否存活
syncLimit=5

#(这个目录可以自行指定)
dataDir=/zookeeper/zk1

#客户端访问zk的端口
clientPort=2181
#master对应于前面在hosts里面配置的主机映射 2888是数据同步和消息传递端口，3888是选举端口
server.1=master:2888:3888 

#master对应于前面在hosts里面配置的主机映射 2889是数据同步和消息传递端口，3889是选举端口 server.2=master:2889:3889 #master对应于前面在hosts里面配置的主机映射 2890是数据同步和消息传递端口，3890是选举端口 server.3=master:2890:3890
 
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 2.4 复制zoo1.cfg为zoo2.cfg添加如下配置

```
cp zoo1.cfg zoo2.cfg
vim zoo2.cfg
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
#session的会话时间 以ms为单位
tickTime=2000

#服务器启动以后，master和slave通讯的时间
initLimit=10

#master和slave之间的心跳检测时间，检测slave是否存活
syncLimit=5

#(这个目录可以自行指定)
dataDir=/zookeeper/zk2

#客户端访问zk的端口
clientPort=2182

#master对应于前面在hosts里面配置的主机映射 2888是数据同步和消息传递端口，3888是选举端口
server.1=master:2888:3888 

#master对应于前面在hosts里面配置的主机映射 2889是数据同步和消息传递端口，3889是选举端口 server.2=master:2889:3889 #master对应于前面在hosts里面配置的主机映射 2890是数据同步和消息传递端口，3890是选举端口 server.3=master:2890:3890
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

###  2.5 复制zoo1.cfg为zoo3.cfg添加如下配置

```
cp zoo1.cfg zoo3.cfg
vim zoo3.cfg
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
#session的会话时间 以ms为单位
tickTime=2000

#服务器启动以后，master和slave通讯的时间
initLimit=10

#master和slave之间的心跳检测时间，检测slave是否存活
syncLimit=5

#(这个目录可以自行指定)
dataDir=/zookeeper/zk3

#客户端访问zk的端口
clientPort=2183
#master对应于前面在hosts里面配置的主机映射 2888是数据同步和消息传递端口，3888是选举端口
server.1=master:2888:3888 

#master对应于前面在hosts里面配置的主机映射 2889是数据同步和消息传递端口，3889是选举端口 server.2=master:2889:3889 #master对应于前面在hosts里面配置的主机映射 2890是数据同步和消息传递端口，3890是选举端口 server.3=master:2890:3890
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 2.6 手动建立myid文件且指定在zk数据目录，也就是dataDir指定的路径（不管真分布还是伪分布都需要指定） 

```
echo 1 >> /zookeeper/zk1/myid
echo 2 >> /zookeeper/zk2/myid
echo 3 >> /zookeeper/zk3/myid
```

 **说明：**

**myid文件内容分别为1,2,3对应于zk配置文件的server.1,server.2,server.3**

### 2.7 测试zookeeper分布式是否搭建成功

分别启动三个zookeeper

```
cd /software/zookeeper-3.4.6/bin/
./zkServer.sh start /software/zookeeper-3.4.6/conf/zoo1.cfg
./zkServer.sh start /software/zookeeper-3.4.6/conf/zoo2.cfg
./zkServer.sh start /software/zookeeper-3.4.6/conf/zoo3.cfg
```

![img](https://images2018.cnblogs.com/blog/1227483/201808/1227483-20180831010659701-368489660.png)

查看3个zookeeper的状态

```
cd /software/zookeeper-3.4.6/bin/
/zkServer.sh status /software/zookeeper-3.4.6/conf/zoo1.cfg
/zkServer.sh status /software/zookeeper-3.4.6/conf/zoo2.cfg
/zkServer.sh status /software/zookeeper-3.4.6/conf/zoo3.cfg
```

 ![img](https://images2018.cnblogs.com/blog/1227483/201808/1227483-20180831204759111-1838405861.png)

可以看到第2个zookeeper是leader，第1个和第3个为follower，**到此zookeeper分布式搭建完成！！！**

### 2.8 Zookeeper常用命令 

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
启动：
./zkServer.sh start /software/zookeeper-3.4.6/conf/zoo1.cfg
./zkServer.sh start /software/zookeeper-3.4.6/conf/zoo2.cfg
./zkServer.sh start /software/zookeeper-3.4.6/conf/zoo3.cfg

停止：
./zkServer.sh stop /software/zookeeper-3.4.6/conf/zoo1.cfg
./zkServer.sh stop /software/zookeeper-3.4.6/conf/zoo2.cfg
./zkServer.sh stop /software/zookeeper-3.4.6/conf/zoo3.cfg

查看状态：
/zkServer.sh status /software/zookeeper-3.4.6/conf/zoo1.cfg
/zkServer.sh status /software/zookeeper-3.4.6/conf/zoo2.cfg
/zkServer.sh status /software/zookeeper-3.4.6/conf/zoo3.cfg
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 2.9 Zookeeper真分布式部署在master、slave1、slave2 三台不同的服务器上

 server1..../conf/zoo.cfg

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/zookeeper/zk
clientPort=2181

server.1=master:2888:3888
server.2=slave1:2888:3888
server.3=slave2:2888:3888
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 server2..../conf/zoo.cfg

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/zookeeper/zk
clientPort=2181

server.1=master:2888:3888
server.2=slave1:2888:3888
server.3=slave2:2888:3888
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

server3..../conf/zoo.cfg

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/zookeeper/zk
clientPort=2181

server.1=master:2888:3888
server.2=slave1:2888:3888
server.3=slave2:2888:3888
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**注意：**

真分布必须是奇数台，因为必须满足n/2+1=m,m必须大于部署zk机器数的一半(n/2)可用才认为集群可用，而且奇数台更节省资源
2/2+1=2 一台不能坏
3/2+1=2 可以坏掉一台

4/2+1=3 只能坏一台
5/2+1=3 可以坏2台

# 三、ZK Shell的使用

## 1. 连接zk

```
zkCli.sh [-server ip:port]
```

**示例：**

连接到master（127.0.0.1 master）这台机器上2181的zookeeper

```
./zkCli.sh -server master 2181
```

![img](https://images2018.cnblogs.com/blog/1227483/201809/1227483-20180901121611600-1825758968.png)

## 2. 列出zk中的节点

```
ls、ls2
```

 **示例：**

 ![img](https://images2018.cnblogs.com/blog/1227483/201809/1227483-20180901121725424-1441930958.png)

## 3. 创建节点

```
create [-s] [-e] path data acl
```

**说明：**

zk的节点分为两种：临时节点（随着zk session消亡而自动删除）、持久节点（一直存在）

-s: 顺序节点

-e: 临时节点

acl：访问权限控制

**示例：**

### 3.1 创建一个zk-test的节点，数据是123 不带-s 、-e默认是持久节点

```
create /zk-test "123"
```

![img](https://images2018.cnblogs.com/blog/1227483/201809/1227483-20180901122233887-1356377352.png)

### 3.2 创建顺序节点

```
create  -s   /zk-test  “test123”
```

 ![img](https://images2018.cnblogs.com/blog/1227483/201809/1227483-20180901122414502-87274781.png)

 多次执行创建顺序节点的命令，可以看到zookeeper的内部会对顺序节点的名称进行自增控制

 ![img](https://images2018.cnblogs.com/blog/1227483/201809/1227483-20180901122536973-1714530808.png)

### 3.3 创建临时节点

```
create -e /zk-test123 "test1234"
```

 ![img](https://images2018.cnblogs.com/blog/1227483/201809/1227483-20180901122904582-118341002.png)

**说明：**

临时节点依赖于顺序节点，临时节点下不能再创建临时节点，顺序节点下才可以创建临时节点

### 3.4 创建子节点test

```
create  /zk-test ""
create  /zk-test/test "1"
ls /zk-test/test
```

![img](https://images2018.cnblogs.com/blog/1227483/201809/1227483-20180901124651262-1830355488.png)

## 4. 删除节点命令

```
delete 
```

**示例：**

删除节点zk-test，如果zk-test下面还有子节点得先删除子节点，才能删除zk-test

```
delete /zk-test/test
delete /zk-test
```

 递归删除一个znode

```
rmr path 
```

示例：

删除zk-test及其下面的子节点

rmr /zk-test

## 5. 获取节点信息

```
get
```

 **示例：**

```
create /zk-123  “abc”
get /zk-123
```

![img](https://images2018.cnblogs.com/blog/1227483/201809/1227483-20180901125326134-2070425505.png)

结果说明：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
结果
cZxid = #创建节点时zk内部自己分配的id
Ctime = #创建节点的时间
mZxid = #修改的id
mtime = 修改的时间
pZxid = 子节点最后一次被修改的id
cVersion = 0  #拥有的子节点被改的话，该值随着改变
dataVersion = 0 #数据版本
aclVersion = 0 # 访问控制权限的版本
ephemeralOwner = 0X0 #临时节点还是持久节点 临时节点值不为0（值为当前会话id），持久节点值永远为0
dataLength = 3 #数据长度
numChildren = 0 #子节点个数
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 6. 更新节点数据

```
set  /zk-123 “d”
```

 ![img](https://images2018.cnblogs.com/blog/1227483/201809/1227483-20180901125516178-1384187109.png)

## 7. 连接到指定节点

```
connect host:port
```

 **示例：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
连接到2181
connect master:2181

连接到2182
connect master:2182

连接到2183
connect master:2183
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**说明：**

**这个命令是在已经连接到zookeeper之后，在里面切换到其他zookeeper时使用** 

 ![img](https://images2018.cnblogs.com/blog/1227483/201809/1227483-20180901130152433-1066596844.png)

## 8. 设置配额

配额：给某个目录指定多少存储空间或者允许创建多少个节点

```
setquota -n|-b val path
```

**参数说明：**

n 指定可以设置多少个子节点

b 指定可以设置多大空间（byte）

**示例：**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
setquota -n 5 /zk-123
create  /zk-123/1 1
create  /zk-123/2 2
create  /zk-123/3 3
create  /zk-123/4 4 
create  /zk-123/5 5 
create  /zk-123/6 6
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**说明：**

对于配额不是硬性的提示，超过配额还是可以继续创建，只不过在日志里面有提示

**配额的用途：**

限制子节点的创建个数和分配空间的大小，如指定某个session有多少空间可以用

## 9. 查看配额

```
listquota path
```

## 10. 查看节点的状态

```
stat path
```

**示例：**

查看节点zk-123的状态

```
stat  /zk-123
```