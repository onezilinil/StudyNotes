# Dubbo学习

## 一、基本概念

### 1. Dubbo是什么

Dubbo是阿里巴巴公司开源的一个高性能优秀的服务框架，使得应用可通过高性能的RPC实现服务的输出和输入功能，可以和Spring框架无缝集成。简单地说，dubbo是一个基于Spring的RPC（远程过程调用）框架，能够实现服务的远程调用、服务的治理，最主要就是服务之间的治理（治理基本上要做成运行时治理）。

> Dubbo是一个远程服务调用的框架，是资源调度和治理中心的管理工具。

### 2. 系统间的远程通信

实现远程通信的几种方式：

* Webservice：基于soap协议，效率不高，项目中不推荐使用。
* RestFul：http+json形式的架构标准，如果项目中服务太多，服务之间调用关系混乱，需要治疗服务。
* Dubbo：使用rpc协议进行远程调用，直接使用socket通信，传输效率高，并且可以统计系统之间的调用关系、调用次数。

### 3. 架构的演变过程

![程序架构的比较](F:\学习笔记\Dubbo\程序架构的演变.png)

* 单一应用架构
  * 当网站流量很小时，只需要一个应用，将所有功能都部署在一起，可以减少部署的节点和成本。
  * 用于简化增删改查工作量的数据访问框架（ORM）是关键。
* 垂直应用架构
  * 当访问量逐渐增大，单一应用增加机器带来的加速度越来越小，将应用拆成互不相干的几个应用，以提高效率。
  * 此时，用于加速前端页面开发的web框架（MVC）是关键
* 分布式服务架构
  * 当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，使前端应用能更快的响应多变的市场需求。
  * 此时，用于提高业务复合及整合的分布式服务框架（RPC）是关键。
* 流动计算架构
  * 当服务越来越多，容量的评估，小服务资源的浪费等问题逐渐显现，此时需增加一个调度中心基于访问压力实时管理集群容量，提高集群利用率。
  * 此时用于提高机器利用率的资源调度和治理中心（SOA）是关键。

[SOA架构是什么](https://blog.csdn.net/u013343616/article/details/79460398)

### 4. Dubbo的好处

* 透明化的远程方法调用，就像调用本地方法一样调用远程方法，只需简单配置，没有任何API侵入，只需用Spring加载Dubbo的配置即可，Dubbo基于Spring的Schema扩展进行加载。
* 软负载均衡及容错机制，可在内网替代F5等硬件负载均衡器，降低成本，减少单点。
* 服务自动注册与发现，不再需要写死服务提供方地址，注册中心基于接口名查询服务提供者的IP地址，并且能够平滑添加或删除服务提供者。

[dubbo专题](https://www.cnblogs.com/a8457013/p/7818925.html)

## 二、Dubbo的基本配置

### 1. 单一工程的Spring的配置

```xml
<bean id="xxxService" class="com.xxx.xxxServiceImpl"/>
<bean id="xxxxAction" class="com.xxx.xxxAction">
    <property name="xxxService" ref="xxxService"/>
</bean>
```

### 2. 远程服务的Spring的配置

Dubbo采用全Spring配置方式，透明化接入应用，对应用没有任何的API侵入，只需要在Spring配置文件中配置Dubbo即可，Dubbo基于Spring的Schema扩展进行加载。

#### 2.1 服务提供方和消费方配置

在本地服务的基础上，只需要做简单的配置，就可以完成远程化。将上面的配置拆分成两份，将服务定义部分放在服务提供方remote-provider.xml，将服务引用部分放在服务消费方remote-consumer.xml。

* remote-provider.xml

```xml
<!-- 和本地服务一样实现远程服务 -->
<bean id="xxxService" class="com.xxx.xxxServiceImpl"/>
<!-- 增加暴露远程服务配置 -->
<dubbo:service interface="com.xxx.xxxService" ref="xxxService">
```

* remote-consumer.xml

```xml
<!-- 增加引用远程服务配置 -->
<dubbo:refrence id="xxxService" ref="com.xxx.xxxService"/>
<!-- 和本地服务一样实现远程服务 -->
<bean id="xxxxAction" class="com.xxx.xxxAction">
    <property name="xxxService" ref="xxxService"/>
</bean>
```

#### 2.2 注册中心的配置

注册中心负责服务地址的注册与查找，相当于目录服务，服务提供者和消费者只在启动时与注册中心进行交互，注册中心不转发请求，只是进行服务提供者址的记录并提供给消费者，压力比较小。使用dubbo-2.3.3以上版本，建议使用zookeeper注册中心。

[Zookeeper安装配置](https://www.cnblogs.com/leeSmall/p/9563547.html)

#### 2.3 整合测试

（1）Service

```java
@Service
public class ItemServiceImpl implements ItemService{
    @Autowired
    private ItemMapper itemMapper;
    
    @Override
    public Item getItemById(long itemId){
        Item item = itemMapper.selectByPrimaryKey(itemId);
    }
}
```

（2）导入依赖

服务提供方和消费方都要导入的依赖

```xml
 <!-- 添加dubbo依赖 -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>dubbo</artifactId>
    <version>2.5.3</version>
    <exclusions>
        <exclusion>
            <groupId>org.springframework</groupId>
            <artifactId>spring</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<!-- 添加zk客户端依赖 -->
<dependency>
    <groupId>com.github.sgroschupf</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.1</version>
</dependency>
```

 （3）服务发布方中Spring的application.xml配置文件

```xml
<!-- 提供方应用信息，用于计算依赖关系 -->
<dubbo:application name="hello-world-app"  />
        
<!-- 使用multicast广播注册中心暴露服务地址 -->
<dubbo:registry address="multicast://224.5.6.7:1234" />
// 或者
<!--<dubbo:registry protocol="zookeeper" address="192.168.25.167:2181" />-->

<!-- 用dubbo协议在20880端口暴露服务 -->
<dubbo:protocol name="dubbo" port="20880" />
    
<!-- 和本地bean一样实现服务 -->
<bean id="ItemServiceImpl" class="com.demo.ItemServiceImpl" />
    
<!-- 声明需要暴露的服务接口 -->
<dubbo:service interface="com.demo.ItemService" ref="ItemServiceImpl" />
```

（4）服务接收方中Spring的application.xml配置文件

```xml
<!-- 扫描包扫描@Controller注解 -->
<context:component-scan base-package="com.taobao.controller">
  
<!-- 注解映射器和适配器配置 -->    
<mvc:annotation-driven/>
    
<!-- 配置ViewResolver -->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/"/> 
    <property name="suffix" value=".jsp"/> 
</bean> 
    
<!-- 消费方应用名，用于计算依赖关系，不是匹配条件，不要与提供方一样 -->
<dubbo:application name="consumer-of-helloworld-app"  />
 
<!-- 使用multicast广播注册中心暴露发现服务地址 -->
<dubbo:registry address="multicast://224.5.6.7:1234" />
// 或者
<!--<dubbo:registry protocol="zookeeper" address="192.168.25.167:2181" />-->
 
<!-- 生成远程服务代理，可以和本地bean一样使用demoService -->
<dubbo:reference id="demoService" interface="com.alibaba.dubbo.demo.DemoService" />
```

（5）Controller

```java
public class ItemController{
	@AutoWired
	private ItemService itemService;
	
	@RequestMapping("/item/{itemID}")
	@RespinseBody
	public Item getItemById(@PatchVariable Long itemID){
		return itemService.getItemById(itemID);
	}
}
```

## 三、Dubbo的深入了解

### 1. Dubbo的架构

![Dubbo的架构](F:\学习笔记\Dubbo\Dubbo的架构.png)

#### 1.1 节点角色说明

* Provider：暴露服务的服务提供方
* Consumer：调用远程服务的服务消费方
* Registry：服务注册与发现的注册中心
* Monitor：统计服务的调用次数和调用时间的监控中心
* Container：服务运行容器

#### 1.2 调用关系说明

* 0：服务容器负责启动、加载、运行服务提供者。
* 1：服务提供者在启动时，向注册中心注册自己提供的服务。
* 2：服务消费者在启动时，向注册中心订阅自己所需的服务。
* 3：注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
* 4：服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败再选另一台调用。
* 5：服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

