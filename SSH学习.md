# Hibernate学习

## 一、基本概念

### 1. Hibernate是什么？

Hibernate框架是一种对象关系映射的框架（Object Relation Mapping），用来在Java对象和关系型数据库之间建立某种映射，可以直接使用Java对象来存取数据，使得Java程序员在不熟悉sql语句的前提下也可以使用对象的编程思维来操纵数据库。

### 2. Hibernate有什么用

传统的几种和数据库交互方式：JDBC、DBCP、C3P0，以上都是操作javabean中的数据或属性（传入时获取每个属性值，获取时把结果组合，拼合成一个JavaBean对象）。

Hibernate的优点：

* 可以建立映射关系，让JavaBean对象和关系型数据库中的列形成映射，简化开发。
* Hibernate对JDBC访问数据库的代码做了封装，大大简化了数据访问层繁琐的重复代码。
* 可以支持多种关系型数据库，从一对一、一对多到多对多的关系。

## 二、Hibernate的基本配置

[jar包下载](http://sourceforge.net/projects/hibernate/files/hibernate-orm/5.0.7.Final/hibernate-release-5.0.7.Final.zip/download)

### 1. 编写Hibernate核心的配置文件

```xml
// 1. 在src目录下，创建名称为hibernate.cfg.xml的配置文件
// 2. 在XML中引入DTD约束
	<!DOCTYPE hibernate-configuration PUBLIC
		"-//Hibernate/Hibernate Configuration DTD 3.0//EN"
		"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">

// 3. 打开：/hibernate-release-5.0.7.Final/project/etc/hibernate.properties，可以查看具体的配置信息	
	* 必须配置的4大参数					
		#hibernate.connection.driver_class com.mysql.jdbc.Driver
		#hibernate.connection.url jdbc:mysql:///test
		#hibernate.connection.username gavin
		#hibernate.connection.password
	
	* 数据库的方言（必须配置的）
		#hibernate.dialect org.hibernate.dialect.MySQLDialect
	
	* 可选的配置
		#hibernate.show_sql true
		#hibernate.format_sql true
		#hibernate.hbm2ddl.auto update
	
	* 引入映射配置文件（一定要注意，要引入映射文件，框架需要加载映射文件）
		* <mapping resource="com/itheima/domain/Customer.hbm.xml"/>				

4. 具体的配置如下
	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE hibernate-configuration PUBLIC
		"-//Hibernate/Hibernate Configuration DTD 3.0//EN"
		"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
	
	<hibernate-configuration>
		<session-factory>
			<property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
			<property name="hibernate.connection.url">jdbc:mysql:///hibernate_day01</property>
			<property name="hibernate.connection.username">root</property>
			<property name="hibernate.connection.password">root</property>
			<property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
			// 引入javabean的配置文件
			<mapping resource="com/itheima/domain/Customer.hbm.xml"/>
		</session-factory>
	</hibernate-configuration>
```

### 2. 编写映射的配置文件

```xml
// 1. 在JavaBean所在的包下创建映射的配置文件
	* 默认的命名规则为：实体类名.hbm.xml
	* 在xml配置文件中引入约束（引入的是hibernate3.0的dtd约束，不要引入4的约束）
		<!DOCTYPE hibernate-mapping PUBLIC 
		    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
		    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">

// 2. 如果不能上网，编写配置文件是没有提示的，需要自己来配置
	* 先复制http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd --> window --> preferences --> 搜索xml --> 选择xml catalog --> 点击add --> 现在URI --> 粘贴复制的地址 --> 选择location，选择本地的DTD的路径

// 3. 编写映射的配置文件
	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE hibernate-mapping PUBLIC 
	    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
	    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
	
	<hibernate-mapping>
		<class name="com.itheima.domain.Customer" table="cst_customer">
			<id name="cust_id" column="cust_id">
				<generator class="native"/>
			</id>
			<property name="cust_name" column="cust_name"/>
			<property name="cust_user_id" column="cust_user_id"/>
			<property name="cust_create_id" column="cust_create_id"/>
			<property name="cust_source" column="cust_source"/>
			<property name="cust_industry" column="cust_industry"/>
			<property name="cust_level" column="cust_level"/>
			<property name="cust_linkman" column="cust_linkman"/>
			<property name="cust_phone" column="cust_phone"/>
			<property name="cust_mobile" column="cust_mobile"/>
		</class>
	</hibernate-mapping>
```

### 3. 编写Hibernate入门代码

```java
1. 具体的代码如下
	/**
	 * 测试保存客户
	 */
	@Test
	public void testSave(){
		// 先加载配置文件
		Configuration config = new Configuration();
		// 默认加载src目录下的配置文件
		config.configure();
		// 创建SessionFactory对象
		SessionFactory factory = config.buildSessionFactory();
		// 创建session对象
		Session session = factory.openSession();
		// 开启事务
		Transaction tr = session.beginTransaction();
		// 编写javabean的保存代码
		Customer c = new Customer();
		// c.setCust_id(cust_id);	已经自动递增
		c.setCust_name("测试名称");
		c.setCust_mobile("110");
		// 保存客户
		session.save(c);
		// 提交事务
		tr.commit();
		// 释放资源
		session.close();
		factory.close();
	}
```

### 4. Hibernate配置文件属性解析

#### 4.1 Hibernate映射常用的配置文件属性

```
* <class>标签		-- 用来将类与数据库表建立映射关系
 name			-- 类的全路径
 table			-- 表名.(类名与表名一致,那么table属性也可以省略)
 catalog		-- 数据库的名称，基本上都会省略不写
* <id>标签			-- 用来将类中的属性与表中的主键建立映射，id标签就是用来配置主键的。
 name			-- 类中属性名
 column 		-- 表中的字段名.(如果类中的属性名与表中的字段名一致,那么column可以省略)
 length		-- 字段的长度，如果数据库已经创建好了，那么length可以不写。如果没有创建好，生成表结构时，length最好指定。
* <property>		-- 用来将类中的普通属性与表中的字段建立映射.
 name			-- 类中属性名
 column		-- 表中的字段名.(如果类中的属性名与表中的字段名一致,那么column可以省略)
 length		-- 数据长度
 type			-- 数据类型（一般都不需要编写，如果写需要按着规则来编写）
    - Hibernate的数据类型	type="string"
    - Java的数据类型		type="java.lang.String"
    - 数据库字段的数据类型	<column name="name" sql-type="varchar"/>
```

#### 4.2 Hibernate核心配置文件的基本属性

```xml
// 1. 核心配置文件的两种方式
	* 第一种方式是属性文件的形式，即properties的配置文件
		* hibernate.properties
			* hibernate.connection.driver_class=com.mysql.jdbc.Driver
		* 缺点
			* 不能加载映射的配置文件，需要手动编写代码去加载
	
	* 第二种方式是XML文件的形式，开发基本都会选择这种方式
		* hibernate.cfg.xml
			* <property name="hibernate.connection.driver_class" >com.mysql.jdbc.Driver</property>
		* 优点
			* 格式比较清晰
			* 编写有提示
			* 可以在该配置文件中加载映射的配置文件（最主要的）

// 2. 关于hibernate.cfg.xml的配置文件方式
	* 必须有的配置
		* 数据库连接信息:
			hibernate.connection.driver_class  			-- 连接数据库驱动程序
			hibernate.connection.url   					-- 连接数据库URL
			hibernate.connection.username  				-- 数据库用户名
			hibernate.connection.password   			-- 数据库密码
		
		* 方言:
			hibernate.dialect   						-- 操作数据库方言
	
	* 可选的配置
		* hibernate.show_sql							-- 显示SQL
		* hibernate.format_sql							-- 格式化SQL
		* hibernate.hbm2ddl.auto						-- 通过映射转成DDL语句
			* create				-- 每次都会创建一个新的表.---测试的时候
			* create-drop			-- 每次都会创建一个新的表,当执行结束之后,将创建的这个表删除.---测试的时候
			* update				-- 如果有表,使用原来的表.没有表,创建一个新的表.同时更新表结构.
			* validate				-- 如果有表,使用原来的表.同时校验映射文件与表中字段是否一致如果不一致就会报错.
	
	* 加载映射
		* 如果XML方式：<mapping resource="cn/itcast/hibernate/domain/User.hbm.xml" />
```

### 5. Hibernate常用的接口和类

#### 5.1 Configuration类

Configuration对象用于配置并且启动Hibernate。Hibernate应用通过该对象来获得对象-关系映射文件中的元数据，以及动态配置Hibernate的属性，然后创建SessionFactory对象。简单一句话：加载Hibernate的配置文件，可以获取SessionFactory对象。

Configuration类的其他应用（了解）：

* 加载配置文件的种类，Hibernate支持xml和properties类型的配置文件，在开发中基本都使用XML配置文件的方式。

* 如果采用的是properties的配置文件，那么通过Configuration configuration = new Configuration();就可以假装配置文件。
	* 但是需要自己手动加载映射文件
	* 例如：config.addResource("cn/itcast/domain/Student.hbm.xml");

* 如果采用的XML的配置文件，通过Configuration configuration = new Configuration().configure();加载配置文件

#### 5.2 SessionFactory类

SessionFactory类是工厂类，是生成Session对象的工厂类，SessionFactory类的特点：

* 由Configuration通过加载配置文件创建该对象。
* SessionFactory对象中保存了当前的数据库配置信息和所有映射关系以及预定义的SQL语句。同时，SessionFactory还负责维护Hibernate的二级缓存。
	* 预定义SQL语句
		* 使用Configuration类创建了SessionFactory对象时，已经在SessionFacotry对象中缓存了一些SQL语句
		* 常见的SQL语句是增删改查（通过主键来查询）
		* 这样做的目的是效率更高

* 一个SessionFactory实例对应一个数据库，应用从该对象中获得Session实例。
* SessionFactory是线程安全的，意味着它的一个实例可以被应用的多个线程共享。
* SessionFactory是重量级的，意味着不能随意创建或销毁它的实例。如果只访问一个数据库，只需要创建一个SessionFactory实例，且在应用初始化的时候完成。
* SessionFactory需要一个较大的缓存，用来存放预定义的SQL语句及实体的映射信息。另外可以配置一个缓存插件，这个插件被称之为Hibernate的二级缓存，被多线程所共享总结

注意：一般应用使用一个SessionFactory,最好是应用启动时就完成初始化。

#### 5.3 Session接口

（1）基本介绍

* Session是在Hibernate中使用最频繁的接口。也被称之为持久化管理器。它提供了和持久化有关的操作，比如添加、修改、删除、加载和查询实体对象
* Session 是应用程序与数据库之间交互操作的一个单线程对象，是 Hibernate 运作的中心
* Session是线程不安全的
* 所有持久化对象必须在 session 的管理下才可以进行持久化操作
* Session 对象有一个一级缓存，显式执行 flush 之前，所有的持久化操作的数据都缓存在 session 对象处
* 持久化类与 Session 关联起来后就具有了持久化的能力

（2）特点

* 不是线程安全的。应避免多个线程使用同一个Session实例
* Session是轻量级的，它的创建和销毁不会消耗太多的资源。应为每次客户请求分配独立的Session实例
* Session有一个缓存，被称之为Hibernate的一级缓存。每个Session实例都有自己的缓存

（3）常用的方法

* save(obj)
* delete(obj)  
* get(Class,id)
* update(obj)
* saveOrUpdate(obj)					-- 保存或者修改（如果没有数据，保存数据。如果有，修改数据）
* createQuery() 					-- HQL语句的查询的方式

#### 5.4 Transction接口

Transaction是事务的接口，Hibernate框架默认情况下事务不自动提交，需要手动提交事务，如果没有开启事务，那么每个Session的操作，都相当于一个独立的事务。常用的方法：

* commit()				-- 提交事务
* rollback()			-- 回滚事务

## 三、Hibernate的深入了解

### 1. 主键的生成策略

持久化类的映射文件的主键id下的节点 <generator class="native"/>，有以下取值：

* increment：适用于short、int、long作为主键，不是使用的数据库自动增长机制。Hibernate中提供的一种增长机制。先进行查询：select max(id) from user；再进行插入，获得最大值+1作为新的记录的主键。
  * 注意：不能在集群环境下或者有并发访问的情况下使用。

* identity：适用于short、int、long作为主键。但是这个必须使用在有自动增长数据库中，采用的是数据库底层的自动增长机制。底层使用的是数据库的自动增长(auto_increment)，像Oracle数据库没有自动增长。

* sequence：适用于short、int、long作为主键.底层使用的是序列的增长方式，Oracle数据库底层没有自动增长,想自动增长需要使用序列。

* uuid:适用于char、varchar类型的作为主键，使用随机的字符串作为主键。

* native：本地策略，根据底层的数据库不同，自动选择适用于该种数据库的生成策略.(short、int、long)。如果底层使用的MySQL数据库，相当于identity；如果底层使用Oracle数据库，相当于sequence.。

* assigned：主键的生成不用Hibernate管理了，必须手动设置主键。	

### 2. 持久化对象的状态

![三个状态转换](./SSH/三个状态转换.bmp)

#### 2.1 Hibernate的持久化类

持久化类：Java类与数据库的某个表建立了映射关系，这个类就称为是持久化类（持久化类 = Java类 + hbm的配置文件）。

#### 2.2 Hibernate的持久化类的状态

Hibernate为了管理持久化类，将持久化类分成了三个状态：
* 瞬时态（Transient  Object）：没有持久化标识OID, 没有被纳入到Session对象的管理。
	
* 持久态（Persistent Object）：有持久化标识OID,已经被纳入到Session对象的管理。
	
* 脱管态（Detached Object）：有持久化标识OID,没有被纳入到Session对象的管理。

### 3. Session对象的一级缓存

#### 3.1 什么是缓存？

其实就是一块内存空间,将数据源（数据库或者文件）中的数据存放到缓存中。再次获取的时候 ，直接从缓存中获取，可以提升程序的性能！

#### 3.2 Hibernate框架提供了两种缓存

* 一级缓存：自带的不可卸载的，一级缓存的生命周期与session一致，一级缓存称为session级别的缓存。

* 二级缓存：默认没有开启，需要手动配置才可以使用的。二级缓存可以在多个session中共享数据，二级缓存称为是sessionFactory级别的缓存。

#### 3.3 Session对象的缓存概述

* Session接口中,有一系列的java的集合,这些java集合构成了Session级别的缓存(一级缓存).将对象存入到一级缓存中,session没有结束生命周期,那么对象在session中存放着。

* 内存中包含Session实例 --> Session的缓存（一些集合） --> 集合中包含的是缓存对象！

#### 3.4 证明一级缓存的存在

在同一个Session对象中两次查询，看有没有进行了两次查询，可以证明使用了缓存。

#### 3.5 数据发生变化时进行同步操作

Hibernate框架是如何做到数据发生变化时进行同步操作的原理：

* 使用get方法查询User对象
* 然后设置User对象的一个属性，注意：没有做update操作。发现，数据库中的记录也改变了。
*  利用快照机制来完成的（SnapShot）

#### 3.6 快照机制机制和原理

会将查询到的数据存在session的缓存区域，并会另外存一份在session的快照区域，当修改了对应的缓存区域中JavaBean对象后，提交之前，会对比缓存和快照中的数据是否一致，不一致的话就会把数据库和快照中的数据进行修改，因此不用进行提交操作也可以修改数据库中的数据。

![](./SSH/Session的快照机制.bmp)

### 4. 事务相关的概念

#### 4.1 什么是事务

事务就是逻辑上的一组操作，组成事务的各个执行单元，操作要么全都成功，要么全都失败。

#### 4.2 事务的特性

* 原子性：事务不可分割。
* 一致性：事务执行的前后数据的完整性保持一致。
* 隔离性：一个事务执行的过程中，不应该受到其他的事务的干扰。
* 持久性：事务一旦提交，数据就永久保持到数据库中。

#### 4.3 不考虑隔离性引发一些读的问题

* 脏读：一个事务读到了另一个事务未提交的数据。
* 不可重复读：一个事务读到了另一个事务已经提交的update数据，导致多次查询结果不一致。
* 虚读：一个事务读到了另一个事务已经提交的insert数据，导致多次查询结构不一致。

#### 4.4 通过设置数据库的隔离级别

* 未提交读：以上的读的问题都有可能发生。
* 已提交读：避免脏读，但是不可重复读，虚读都有可能发生。
* 可重复读：避免脏读，不可重复读，但是虚读是有可能发生。
* 串行化：以上读的情况都可以避免。

#### 4.5 Hibernate的框架中设置隔离级别

在Hibernate的框架中来设置隔离级别，需要在hibernate.cfg.xml的配置文件中通过ibernate.connection.isolation标签来配 置，取值：

* 1—Read uncommitted isolation
* 2—Read committed isolation
* 4—Repeatable read isolation
* 8—Serializable isolation

### 5. 丢失更新的问题

如果不考虑隔离性，也会产生写入数据的问题，这一类的问题叫丢失更新的问题。

例如：两个事务同时对某一条记录做修改，就会引发丢失更新的问题。
* A事务和B事务同时获取到一条数据，同时再做修改
* 如果A事务修改完成后，提交了事务
* B事务修改完成后，不管是提交还是回滚，如果不做处理，都会对数据产生影响

（1）解决方案有两种：
* 悲观锁：
	* 采用的是数据库提供的一种锁机制，如果采用做了这种机制，在SQL语句的后面添加 for update 子句
		* 当A事务在操作该条记录时，会把该条记录锁起来，其他事务是不能操作这条记录的。
		* 只有当A事务提交后，锁释放了，其他事务才能操作该条记录

* 乐观锁：
	* 采用版本号的机制来解决的。会给表结构添加一个字段version=0，默认值是0
		* 当A事务在操作完该条记录，提交事务时，会先检查版本号，如果发生版本号的值相同时，才可以提交事务。同时会更新版本号version=1。
		* 当B事务操作完该条记录时，提交事务时，会先检查版本号，如果发现版本不同时，程序会出现错误。

（2）使用Hibernate框架解决丢失更新的问题
* 悲观锁：
	* 使用session.get(Customer.class, 1,LockMode.UPGRADE); 方法

* 乐观锁：
	* 在对应的JavaBean中添加一个属性，名称可以是任意的。例如：private Integer version; 提供get和set方法
	* 在映射的配置文件中，提供<version name="version"/>标签即可。

### 6. 绑定本地的Session

（1）之前在讲JavaWEB的事务的时候，需要在业务层使用Connection来开启事务。
* 一种是通过参数的方式传递下去
* 另一种是把Connection绑定到ThreadLocal对象中

（2）现在的Hibernate框架中，使用session对象开启事务，所以需要来传递session对象，框架提供了ThreadLocal的方式
* 需要在hibernate.cfg.xml的配置文件中提供配置
	
```
	<property name="hibernate.current_session_context_class">thread</property>
```

* 重新HibernateUtil的工具类，使用SessionFactory的getCurrentSession()方法，获取当前的Session对象。并且该Session对象不用手动关闭，线程结束了，会自动关闭。
	
	```
	public static Session getCurrentSession(){
	return factory.getCurrentSession();
	}
	```
	
* 注意：想使用getCurrentSession()方法，必须要先配置才能使用。

### 7. Hibernate的查询方式：

#### 7.1 HQL（Hibernate Query language）Query查询接口

（1）HQL基本的查询格式

支持方法链的编程，即直接调用list()方法。简单的代码如下：

```
session.createQuery("from Customer").list();
```

（2）使用别名的方式
```
session.createQuery("from Customer c").list();

session.createQuery("select c from Customer c").list();
```

（3）排序查询

排序查询和SQL语句中的排序的语法是一样的

```
// 升序
session.createQuery("from Customer order by cust_id").list();
// 降序
session.createQuery("from Customer order by cust_id desc").list();
```

（4）分页查询

Hibernate框架提供了分页的方法，咱们可以调用方法来完成分页。两个方法如下：

* setFirstResult(a)		-- 从哪条记录开始，如果查询是从第一条开启，值是0
* setMaxResults(b)		-- 每页查询的记录条数

```
List<LinkMan> list = session.createQuery("from LinkMan").setFirstResult(0).setMaxResults().list();
```

（5）带条件的查询

setParameter("?号的位置，默认从0开始","参数的值"); 不用考虑参数的具体类型

```java
// 1.查询所有记录：
Query query = session.createQuery("from Customer");
List<Customer> list = query.list();
System.out.println(list);

// 2.条件查询：
Query query = session.createQuery("from Customer where name = ?");
query.setString(0, "李健");
List<Customer> list = query.list();
System.out.println(list);

// 3.条件查询：
Query query = session.createQuery("from Customer where name = :aaa and age = :bbb");
query.setString("aaa", "李健");
query.setInteger("bbb", 38);
List<Customer> list = query.list();
System.out.println(list);

// 4.模糊查询
Query query = session.createQuery("from Linkman where lkm_name like ? order by lkm_id desc");
query.setFirstResult(0).setMaxResults(3);
query.setParameter(0, "%熊%");
List<Linkman> list = query.list();
for (Linkman linkman : list) {
	System.out.println(linkman);
}
```

（6）投影查询

投影查询就是想查询某一字段的值或者某几个字段的值

```java
// 如果查询多个字段，不知道持久化对象
List<Object[]> list = session.createQuery("select c.cust_name,c.cust_level from Customer c").list();
for (Object[] objects : list) {
    System.out.println(Arrays.toString(objects));
}

// 可以新建持久化类，将字段封装到指定持久化对象中
List<Customer> list = session.createQuery("select new Customer(c.cust_name,c.cust_level) from Customer c").list();
for (Customer customer : list) {
    System.out.println(customer);
}
```

（7）聚合查询

```java
// 1.获取总的记录数
	Session session = HibernateUtils.getCurrentSession();
	Transaction tr = session.beginTransaction();
	List<Number> list = session.createQuery("select count(c) from Customer c").list();
	Long count = list.get(0).longValue();
	System.out.println(count);
	tr.commit();

// 2.获取某一列数据的和
	Session session = HibernateUtils.getCurrentSession();
	Transaction tr = session.beginTransaction();
	List<Number> list = session.createQuery("select sum(c.cust_id) from Customer c").list();
	Long count = list.get(0).longValue();
	System.out.println(count);
	tr.commit();
```

（8）HQL多表查询

多表的查询使用HQL语句进行查询，HQL语句和SQL语句的查询语法比较类似。

- 内连接查询（相当于交集）
  - 显示内连接
    - select * from customers c inner join orders o on c.cid = o.cno;
  - 隐式内连接
    - select * from customers c,orders o where c.cid = o.cno;

```
// 内连接使用 inner join ，默认返回的是Object数组
Transaction tr = session.beginTransaction();
List<Object[]> list = session.createQuery("from Customer c inner join c.linkmans").list();
for (Object[] objects : list) {
	System.out.println(Arrays.toString(objects));
}
tr.commit();
```

- 外连接查询
  - 左外连接
    - select * from customers c left join orders o on c.cid = o.cno;
  - 右外连接
    - select * from customers c right join orders o on c.cid = o.cno;

```
// 左外连接查询
Session session = HibernateUtils.getCurrentSession();
Transaction tr = session.beginTransaction();
List<Customer> list = session.createQuery("from Customer c left join fetch c.linkmans").list();
Set<Customer> set = new HashSet<Customer>(list);
for (Customer customer : set) {
	System.out.println(customer);
}
tr.commit();
```

- 迫切和非迫切，join fetch
  - 非迫切返回结果是Object[]
  - 迫切连接返回的结果是对象，把客户的信息封装到客户的对象中，把订单的信息封装到客户的Set集合中。

```
// 迫切内连接:inner join fetch ，返回的是实体对象
Transaction tr = session.beginTransaction();
List<Customer> list = session.createQuery("from Customer c inner join fetch c.linkmans").list();
Set<Customer> set = new HashSet<Customer>(list);
for (Customer customer : set) {
	System.out.println(customer);
}
tr.commit();
```

#### 7.2 QBC（Query by Critiria）Criteria查询接口

QBC做条件查询非常合适。

（1）简单查询，使用的是Criteria接口

```
List<Customer> list = session.createCriteria(Customer.class).list();
for (Customer customer : list) {
	System.out.println(customer);
}
```

（2）排序查询

需要使用addOrder()的方法来设置参数，参数使用org.hibernate.criterion.Order对象

```java
Transaction tr = session.beginTransaction();
Criteria criteria = session.createCriteria(Linkman.class);
// 设置排序
criteria.addOrder(Order.desc("lkm_id"));
List<Linkman> list = criteria.list();
for (Linkman linkman : list) {
	System.out.println(linkman);
}
tr.commit();
```

（3）分页查询

QBC的分页查询也是使用两个方法
* setFirstResult();
* setMaxResults();

```java
Transaction tr = session.beginTransaction();
Criteria criteria = session.createCriteria(Linkman.class);
// 设置排序
criteria.addOrder(Order.desc("lkm_id"));
criteria.setFirstResult(0);
criteria.setMaxResults(3);
List<Linkman> list = criteria.list();
for (Linkman linkman : list) {
	System.out.println(linkman);
}
tr.commit();
```

（4）条件查询

Criterion是查询条件的接口，Restrictions类是Hibernate框架提供的工具类，使用该工具类来设置查询条件。条件查询使用Criteria接口的add方法，用来传入条件。使用Restrictions的添加条件的方法，来添加条件，例如：

| 方法                 | 说明     |
| -------------------- | -------- |
| Restrictions.eq      | 相等     |
| Restrictions.gt      | 大于     |
| Restrictions.ge      | 大于等于 |
| Restrictions.lt      | 小于     |
| Restrictions.le      | 小于等于 |
| Restrictions.between | 在之间   |
| Restrictions.like    | 模糊查询 |
| Restrictions.in      | 范围     |
| Restrictions.and     | 并且     |
| Restrictions.or      | 或者     |

```java
// 1.查询所有记录
Criteria criteria = session.createCriteria(Customer.class);
List<Customer> list = criteria.list();
System.out.println(list);

// 2.条件查询
Criteria criteria = session.createCriteria(Customer.class);
criteria.add(Restrictions.eq("name", "李健"));
List<Customer> list = criteria.list();
System.out.println(list);

// 3.条件查询
Criteria criteria = session.createCriteria(Customer.class);
criteria.add(Restrictions.eq("name", "李健"));
criteria.add(Restrictions.eq("age", 38));
List<Customer> list = criteria.list();
System.out.println(list);

// select * from linkman where lkm_gender = '男' or lkm_id = '3' order by lkm_id
Session session = HibernateUtils.getCurrentSession();
Transaction tr = session.beginTransaction();
Criteria criteria = session.createCriteria(Linkman.class);
// 设置排序
criteria.addOrder(Order.desc("lkm_id"));
// 设置查询条件
criteria.add(Restrictions.or(Restrictions.eq("lkm_gender", "男"), Restrictions.gt("lkm_id", 3L)));
List<Linkman> list = criteria.list();
for (Linkman linkman : list) {
	System.out.println(linkman);
}
tr.commit();
```

（5）聚合函数查询

Projection的聚合函数的接口，而Projections是Hibernate提供的工具类，使用该工具类设置聚合函数查询。使用QBC的聚合函数查询，需要使用criteria.setProjection()方法

```java
Session session = HibernateUtils.getCurrentSession();
Transaction tr = session.beginTransaction();
Criteria criteria = session.createCriteria(Linkman.class);
criteria.setProjection(Projections.rowCount());
List<Number> list = criteria.list();
Long count = list.get(0).longValue();
System.out.println(count);
tr.commit();
```

#### 7.3 SQL查询

```
Transaction tr = session.beginTransaction();
SQLQuery sqlQuery = session.createSQLQuery("select * from cst_linkman where lkm_gender = ?");
sqlQuery.setParameter(0,"男");
sqlQuery.addEntity(Linkman.class);
List<Linkman> list = sqlQuery.list();
System.out.println(list);
tr.commit();
```

### 8. Hibernate的级联操作

#### 8.1 数据和配置准备

```xml
// 1. 先导入SQL的建表语句
创建客户和联系人的数据表

// 2.编写客户和联系人的JavaBean程序（注意一对多的编写规则）,客户的JavaBean如下
public class Customer {
    private Long cust_id;
    private String cust_name;
    private Long cust_user_id;
    private Long cust_create_id;
    private String cust_source;
    private String cust_industry;
    private String cust_level;
    private String cust_linkman;
    private String cust_phone;
    private String cust_mobile;
    // 多方在一方中的属性表示
    private Set<Linkman> linkmans = new HashSet<Linkman>();
}
// 联系人的JavaBean如下
public class Linkman {
    private Long lkm_id;
    private String lkm_name;
    private String lkm_gender;
    private String lkm_phone;
    private String lkm_mobile;
    private String lkm_email;
    private String lkm_qq;
    private String lkm_position;
    private String lkm_memo;
	// 同时多方也要写一方的属性
    private Customer customer;
}

// 3. 编写客户和联系人的映射配置文件（注意一对多的配置编写）
	* 客户的映射配置文件如下
		<class name="com.itheima.domain.Customer" table="cst_customer">
			<id name="cust_id" column="cust_id">
				<generator class="native"/>
			</id>
			<property name="cust_name" column="cust_name"/>
			<property name="cust_user_id" column="cust_user_id"/>
			<property name="cust_create_id" column="cust_create_id"/>
			<property name="cust_source" column="cust_source"/>
			<property name="cust_industry" column="cust_industry"/>
			<property name="cust_level" column="cust_level"/>
			<property name="cust_linkman" column="cust_linkman"/>
			<property name="cust_phone" column="cust_phone"/>
			<property name="cust_mobile" column="cust_mobile"/>
			// lkm_cust_id是多方中的外键字段
			<set name="linkmans">
				<key column="lkm_cust_id"/>
				<one-to-many class="com.itheima.domain.Linkman"/>
			</set>
		</class>
	
	* 联系人的映射配置文件如下
		<class name="com.itheima.domain.Linkman" table="cst_linkman">
			<id name="lkm_id" column="lkm_id">
				<generator class="native"/>
			</id>
			<property name="lkm_name" column="lkm_name"/>
			<property name="lkm_gender" column="lkm_gender"/>
			<property name="lkm_phone" column="lkm_phone"/>
			<property name="lkm_mobile" column="lkm_mobile"/>
			<property name="lkm_email" column="lkm_email"/>
			<property name="lkm_qq" column="lkm_qq"/>
			<property name="lkm_position" column="lkm_position"/>
			<property name="lkm_memo" column="lkm_memo"/>

			<many-to-one name="customer" class="com.itheima.domain.Customer" column="lkm_cust_id"/>
		</class>
```

#### 8.2 级联的属性

| 属性值            | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| none              | 不使用级联                                                   |
| save-update       | 级联保存或更新                                               |
| delete            | 级联删除                                                     |
| delete-orphan     | 孤儿删除(注意：只能应用在一对多关系)                         |
| all               | 除了delete-orphan的所有情况.（包含save-update delete）       |
| all-delete-orphan | 包含了delete-orphan的所有情况.（包含save-update delete delete-orphan） |

#### 8.3 级联保存

在Hibernate中代码只插入其中的一方的数据，那么程序会抛出异常，想完成只保存一方的数据，并且把相关联的数据都保存到数据库中，那么需要配置级联。

```
<many-to-one name="customer" class="com.itheima.domain.Customer" column="lkm_cust_id" cascade="save-update"/>

Customer customer = new Customer();
customer.setName("er部门");

Linkman linkman = new Linkman();
linkman.setName("www");　　　　//这个就是设置相关联的对象
linkman.setCustomer(dept);
//这句话可以有可以没有，具体作用在讲解inverse的时候在说
dept.getStaffSet().add(staff);

//session.save(dept);　　　　//只需要保存staff，就会将dept也一并保存了。
session.save(staff);
```

**注意：**级联保存是方向性，也就是在哪一方配置文件里面配置级联属性，当修改这一方的数据的时候，另外一方的数据也会改变，反过来不成立。

#### 8.4 级联删除

在数据库中直接删除一方的数据时，那么SQL语句是会报出错误。但是如果使用Hibernate框架直接删除客户的时候，测试发现是可以删除的，但是多方不会删除，多方的外键显示为空值，上述的删除是普通的删除，那么也可以使用级联删除。

```
<many-to-one cascade="delete" />
```

**注意：**级联删除也是有方向性的。

> 孤儿删除（孤子删除）：孤儿删除是指在解除了父子关系的时候，将子方记录中外键置为null。没有外键id的多方，只有在一对多的环境下才有孤儿删除，在一对多的关系中，可以将一的一方认为是父方，将多的一方认为是子方。
>
> ```
> <many-to-one cascade="delete-orphan" />
> ```

#### 8.5 外键维护

inverse外键维护，也就是修改一方的数据，多方的数据会进行修改。会将多余的数据缓存于session中，多对多时需要关闭其中的一个，否则会报错。

>inverse 默认值是falase，放在一方，表示让自己维护外键。建议在一对多双向关联关系中，将一方的inverse属性设置为true，即将主外键的关系交由多方来维护。
>
>打个比方：在一个公司中，是老板认识所有的员工容易，还是所有员工认识老板容易？
>
>老板不放弃外键维护，相当于每招聘一个员工，就需要分别insert 和 update 员工的信息一次，insert操作相当于招聘来到这个老板手下工作，老板去update员工的外键表示老板需要认识一下员工，这样及其麻烦；放弃相当于让员工自己去维护外键，去知道谁是自己的上级，老板不需要关心谁是自己下级。这样可以提高性能。
>
>因此建议使用的时候在一方设置 inverse = true，表示让多方维护外键，在多方设置 cascade = save-update，保存的时候只需要去保存多方就可以了。
>
>注意：如果不是保存多方而是保存一方的话，因为一方放弃了外键维护，所以多方会产生多方的数据外键为null的情况。

[hibernate(六) cascade(级联)和inverse关系详解](https://www.cnblogs.com/whgk/p/6135591.html)

[Hibernate inverse的作用是什么](https://blog.csdn.net/qq_39470214/article/details/89057013)

[在 Hibernate 中inverse的属性](https://www.cnblogs.com/zsping/p/5835765.html)

#### 8.6 懒加载

lazy懒加载放在class或者set或者many-to-one标签上，获取代理对象，用到时才会发送SQL语句进行查询，这样可以提高性能。

[Hibernate之lazy延迟加载（转）](https://www.cnblogs.com/zhangzongle/p/5781682.html)

[Hibernate中get()和load()的区别](https://www.cnblogs.com/cc11001100/p/6883790.html)

#### 8.7 迫切连接

fetch迫切连接，用于抓取策略中，可以选择是否加载关联的对象，若是，则使用左外迫切连接方式，获取关联对象的数据，也是提高性能的一种方法。

[hibernate抓取策略fetch](https://blog.csdn.net/u011159417/article/details/75734519)

# Struts2学习

## 一、基本概念

### 1. Struts2是什么

Struts2是一个基于MVC的web框架，相当于view层，不用将路径写死在代码中，更加灵活的简化代码。

### 2. Struts2有什么用

MVC模式是一个servlet只对应一个功能，Struts2可以将封装数据和调用service相结合，易于与其他框架进行整合。

![前端控制器模式](./SSH/前端控制器模式.bmp)

## 二、Struts2的基本配置

[jar包下载](https://struts.apache.org)，Struts2框架的开发jar包非常多，但是不是所有都是必须要引入的，有一些必须要导入的jar包，这些jar包可以从Struts2框架提供的应用中找到。可以打开apps目录，然后找到struts2-blank.war应用解压，打开WEB-INF/lib目录下所以的jar包。复制到工程中。

### 1. struts2包目录结构

> apps	-- Struts2框架提供了一些应用
>
> libs	-- Struts2框架开发的jar包
>
> docs	-- Struts2框架开发文档
>
> src	-- Struts2框架源码

### 2. 编写web.xml文件

修改web.xml中的配置

```xml
// 在web.xml中配置
<filter>
	<filter-name>struts2</filter-name>
	<filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>struts2</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```

**注意：**必须要配置Struts2的前端控制器类org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter，这是Struts2核心的控制器。

### 3. 编写Struts2.xml文件

在src下引入struts.xml配置文件：

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<!DOCTYPE struts PUBLIC
	"-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
	"http://struts.apache.org/dtds/struts-2.3.dtd">

<struts>
    <package name="default" namespace="/" extends="struts-default">
        <action name="hello" class="com.itheima.action.HelloAction" method="sayHello">
        </action>
    </package>
</struts>
```

### 4. 编写Action类

Action类是动作类，是Struts2处理请求，封装数据，响应页面的核心控制器。需要自己编写。

```java
package cn.itcast.action;
	public String sayHello(){
	System.out.println("Hello Struts2!!");
	return null;
}
```

## 三、Struts2的深入了解

### 1. Struts2的执行流程

#### 1.1 Struts2的大概执行流程

* 编写的页面，点击超链接，请求提交到服务器端。
* 请求会先经过Struts2的核心过滤器（StrutsPrepareAndExecuteFilter）
	* 过滤器的功能是完成了一部分代码功能
	* 就是一系列的拦截器执行了，进行一些处理工作。
	* 可以在struts-default.xml配置文件中看到有很多的拦截器。可以通过断点的方式来演示。
	* 拦截器执行完后，会根据struts.xml的配置文件找到请求路径，找到具体的类，通过反射的方式让方法执行。

![执行流程](./SSH/执行流程.bmp)

JSP页面-->StrutsPrepereAndExecuteFilter过滤器-->执行一系列拦截器（完成了部分代码）-->执行到目标Action-->返回字符串-->结果页面（result）-->页面跳转

#### 1.2 Struts2的运行流程图解

![struts2执行流程](./SSH/struts2执行流程.bmp)

[Struts2工作原理和执行流程图]( https://blog.csdn.net/qq_37230121/article/details/80229927 )

### 2. Struts2框架配置文件加载的顺序

#### 2.1 StrutsPrepareAndExecuteFilter过滤器

* Prepare		-- 预处理，加载核心的配置文件

* Execute		-- 执行，让部分拦截器执行

#### 2.2 StrutsPrepareAndExecuteFilter配置文件

通过源代码可以看到具体加载的配置文件和加载配置文件的顺序：

* init_DefaultProperties(); 				-- 加载org/apache/struts2/default.properties

* init_TraditionalXmlConfigurations();		-- 加载struts-default.xml,struts-plugin.xml,struts.xml

* init_LegacyStrutsProperties();			-- 加载自定义的struts.properties.

* init_CustomConfigurationProviders();		-- 加载用户自定义配置提供者

* init_FilterInitParameters() ;				-- 加载web.xml

#### 2.3 重点了解的配置

* default.properties		-- 在org/apache/struts2/目录下，代表的是配置的是Struts2的常量的值

* struts-default.xml		-- 在Struts2的核心包下，代表的是Struts2核心功能的配置（Bean、拦截器、结果类型等）

* struts.xml				-- 重点中的重点配置，代表WEB应用的默认配置，在工作中，基本就配置它就可以了！！（可以配置常量）

* web.xml					-- 配置前端控制器（可以配置常量）

* 注意：
	* 前3个配置文件是struts2框架的默认配置文件，基本不用修改。
	
	* 后3个配置文件可以允许自己修改struts2的常量。但是有一个特点：后加载的配置文件修改的常量的值，会覆盖掉前面修改的常量的值。

#### 2.4 总结

* 先加载default.properties文件，在org/apache/struts2/default.properties文件，都是常量。

* 又加载struts-default.xml配置文件，在核心的jar包最下方，struts2框架的核心功能都是在该配置文件中配置的。

* 再加载struts.xml的配置文件，在src的目录下，代表用户自己配置的配置文件

* 最后加载web.xml的配置文件

* 后加载的配置文件会覆盖掉之前加载的配置文件（在这些配置文件中可以配置常量）

>  注意以下问题：
>
> * default.properties		-- 默认值，咱们是不能修改的
> * struts.xml				-- 可以配置，开发中基本上都在该配置文件中配置常量
> * struts.properties			-- 可以配置，基本不会在该配置文件中配置
> * web.xml					-- 可以配置，基本不会在该配置文件中配置
> * 后加载的配置文件会覆盖掉之前加载的配置

### 3. Struts2的文件属性配置

#### 3.1 package

<package>标签，如果要配置<Action>的标签，那么必须要先配置<package>标签，代表的包的概念。包含的属性：

* name					-- 包的名称，要求是唯一的，管理action配置
* extends				-- 继承，可以继承其他的包，只要继承了，那么该包就包含了其他包的功能，一般都是继承struts-default
* namespace				-- 名称空间，一般与<action>标签中的name属性共同决定访问路径（通俗话：怎么来访问action），常见的配置如下
	* namespace="/"		-- 根名称空间
	* namespace="/aaa"	-- 带有名称的名称空间
* abstract				-- 抽象的。这个属性基本很少使用，值如果是true，那么编写的包是被继承的

#### 3.2 action

<action>标签，代表配置action类，包含的属性

* name			-- 和<package>标签的namespace属性一起来决定访问路径的
* class			-- 配置Action类的全路径（默认值是ActionSupport类）
* method		-- Action类中执行的方法，如果不指定，默认值是execute

#### 3.3 result

`<result>`标签，action类中方法执行，返回的结果跳转的页面

* name		-- 结果页面逻辑视图名称，也就是action方法的返回值
* type		-- 结果类型（默认值是转发，也可以设置其他的值）

### 4. 常量配置

和package标签同级，在Struts.xml中设置一些常量：

* struts.i18n.encoding=UTF-8			-- 指定默认编码集,作用于HttpServletRequest的setCharacterEncoding方法

* struts.action.extension=action,do,ht		-- 该属性指定需要Struts 2处理的请求后缀，该属性的默认值是action，即所有匹配*.action的请求都由Struts2处理。如果用户需要指定多个请求后缀，则多个后缀之间以英文逗号（,）隔开

* struts.serve.static.browserCache=true		-- 设置浏览器是否缓存静态内容,默认值为true(生产环境下使用),开发阶段最好关闭 

* struts.configuration.xml.reload=false		-- 当struts的配置文件修改后,系统是否自动重新加载该文件,默认值为false(生产环境下使用) 

* struts.devMode = false					-- 开发模式下使用,这样可以打印出更详细的错误信息 

```
<constant name="struts.enable.DynamicMethodInvocation" value="true"/>
```

### 5. Action类的三种写法

#### 5.1 普通的POJO类

Action类就是一个POJO类（Plain Ordinary Java Object），简单的Java对象。没有继承某个类，没有实现接口，就是POJO的类。

#### 5.2 实现Action接口

Action接口中定义了5个常量，5个常量的值对应的是5个逻辑视图跳转页面（跳转的页面还是需要自己来配置），还定义了一个方法，execute方法。大家需要掌握5个逻辑视图的返回值常量：

* SUCCESS		-- 成功.
* INPUT			-- 用于数据表单校验.如果校验失败,跳转INPUT视图.
* LOGIN			-- 登录.
* ERROR			-- 错误.
* NONE			-- 页面不转向.

#### 5.3 继承ActionSupport类

ActionSupport本身继承了许多的类，利于编写代码。

### 6. Action访问的三种方式

```java
// Action的代码
public String add(){
	System.out.println("添加图书");
	return NONE;
}
public String delete(){
	System.out.println("删除图书");
	return NONE;
}
```

#### 6.1 传统的配置方式

```xml
<package name="demo2" extends="struts-default" namespace="/">
  	<action name="addBook" class="cn.itcast.demo2.BookAction" method="add"></action>
  	<action name="deleteBook" class="cn.itcast.demo2.BookAction" method="delete"></action>
 </package>

// 链接
<a href="${pageContext.request.contextPath}/addBook.action">添加图书</a>
<a href="${pageContext.request.contextPath}/deleteBook.action">删除图书</a>
```

#### 6.2 通配符的配置方式

通配符的访问方式：访问的路径和方法的名称必须要有某种联系。通配符就是 * 代表任意的字符，使用通配符的方式可以简化配置文件的代码编写，而且扩展和维护比较容易。

```xml
<action name="order_*" class="cn.itcast.demo2.OrderAction" method="{1}"></action>

// 链接
http://localhost/struts2_01/order_add.action
```

> 在JSP页面发送请求，配置文件中的`order_*`可以匹配该请求，`*`就相当于变成了add，method属性的值使用{1}来代替，{1}就表示的是第一个`*`号的位置.。所以method的值就等于了add，那么就找到Action类中的add方法，那么add方法就执行了，通配符的方式可以让一个action配置调配多种符合匹配规则的请求，减少配置的代码，不过注意result还是需要自己配置。

#### 6.3 动态方法访问的方式

如果想完成动态方法访问的方式，需要开启一个常量，struts.enable.DynamicMethodInvocation = false，把值设置成true。

```xml
<constant name="struts.enable.DynamicMethodInvocation" value="true"></constant>

<action name="product" class="cn.itcast.demo2.ProductAction"></action>

<a href="${pageContext.request.contextPath}/product!add.action">添加商品</a>
<a href="${pageContext.request.contextPath}/product!delete.action">删除商品</a>
```

> 使用action的 name+!+方法名的方式，例如product!add的请求方式，这样就可以不用配置method属性，只需要前缀是 action 的 name 是 product 的就都可以进行匹配。不过项目中不常用

### 7. Struts2框架中使用Servlet的API

1. 在Action类中也可以获取到Servlet一些常用的API
	* 需求：提供JSP的表单页面的数据，在Action中使用Servlet的API接收到，然后保存到三个域对象中，最后再显示到JSP的页面上。
		* 提供JSP注册的页面，演示下面这三种方式
	

```html
<h3>注册页面</h3>
<form action="${ pageContext.request.contextPath }/xxx.action" method="post">
	姓名:<input type="text" name="username" /><br/>
	密码:<input type="password" name="password" /><br/>
	<input type="submit" value="注册" />
</form>
```



#### 7.1 完全解耦合的方式

如果使用该种方式，Struts2框架中提供了一个类，ActionContext类，该类中提供一些方法，通过方法获取Servlet的API。一些常用的方法如下：

* static ActionContext getContext()  						-- 通过ActionContext工具类获取ActionContext对象实例

通过ActionContext实例的一些方法：

* java.util.Map<java.lang.String,java.lang.Object> getParameters()  		-- 获取请求参数，相当于request.getParameterMap();

* java.util.Map<java.lang.String,java.lang.Object> getSession()  			-- 获取的代表session域的Map集合，就相当于操作session域。

* java.util.Map<java.lang.String,java.lang.Object> getApplication() 		-- 获取代表application域的Map集合

* void put(java.lang.String key, java.lang.Object value)  					-- 注意：向request域中存入值。

#### 7.2 使用原生Servlet的API的方式

Struts2框架提供了一个类，ServletActionContext，该类中提供了一些静态的方法，具体的方法如下：

* getPageContext()

* getRequest()

* getResponse()

* getServletContext()

### 8. 结果页面的跳转

#### 8.1 结果页面

* 全局结果页面
	
	如果<package>包中的一些action都返回success，并且返回的页面都是同一个JSP页面，这样就可以配置全局的结果页面。全局结果页面针对的当前的包中的所有的Action，但是如果局部还有结果页面，会优先局部的。使用的标签是

```xml
<package>
    <global-results>
        <result>/demo3/suc.jsp</result>
    </global-results>
</package>
```

* 局部结果页面

```
<result>/demo3/suc.jsp</result>
```

#### 8.2 结果页面的类型

结果页面使用<result>标签进行配置，包含两个属性：

* name	-- 逻辑视图的名称，也就是action方法的返回值名称

* type	-- 跳转的类型，值一些，需要掌握一些常用的类型。常见的结果类型去struts-default.xml中查找。

| type属性值     | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| dispatcher     | 转发，type的默认值Action--->JSP                              |
| redirect       | 重定向，Action--->JSP                                        |
| chain          | 多个action之间跳转，从一个Action转发到另一个Action，文本值是action配置的name。Action---Action |
| redirectAction | 多个action之间跳转，从一个Action重定向到另一个Action，文本值是action配置的name。Action---Action |
| stream         | 文件下载时候使用的                                           |

[struts2实现下载各种文件](https://blog.csdn.net/xqhys/article/details/79691210)

### 9. 数据封装

作为MVC框架，必须要负责解析HTTP请求参数，并将其封装到Model对象中。封装数据为开发提供了很多方便，Struts2框架提供了很强大的数据封装的功能，不再需要使用Servlet的API完成手动封装了！！

#### 9.1 属性驱动

提供对应属性的set方法进行数据的封装。

在action类中：

* 表单的哪些属性需要封装数据，那么在对应的Action类中提供该属性的set方法即可。
* 表单中的数据提交，最终找到Action类中的setXxx的方法，最后赋值给全局变量。

在页面上，使用OGNL表达式进行数据封装。

- 在页面中使用OGNL表达式进行数据的封装，就可以直接把属性封装到某一个JavaBean的对象中。
- 在页面中定义一个JavaBean，并且提供set方法：例如：private User user;
- 页面中的编写发生了变化，需要使用OGNL的方式，表单中的写法：

```
<input type="text" name="user.username">
```

缺点：

* Struts2的框架采用的拦截器完成数据的封装。
* 这种方式不是特别好：因为属性特别多，提供特别多的set方法，而且还需要手动将数据存入到对象中。
* 这种情况下，Action类就相当于一个JavaBean，就没有体现出MVC的思想，Action类又封装数据，又接收请求处理，耦合性较高。

#### 9.2 模型驱动

使用模型驱动的方式，也可以把表单中的数据直接封装到一个JavaBean的对象中，并且表单的写法和之前的写法没有区别。编写的页面不需要任何变化，正常编写name属性的值。模型驱动的编写步骤：
* 手动实例化JavaBean，即：private User user = new User();
* 必须实现ModelDriven<T>接口，实现getModel()的方法，在getModel()方法中返回user即可。

```
// 对象的方式
<h1>Struts2的模型驱动驱动中:模型驱动的方式</h1>
<form action="${ pageContext.request.contextPath }/strutsDemo3.action" method="post">
	名称:<input type="text" name="name"><br/>
	年龄:<input type="text" name="age"><br/>
	生日:<input type="text" name="birthday"><br/>
	<input type="submit" value="提交">
</form>

// Action：
public class StrutsDemo3 extends ActionSupport implements ModelDriven<User>{
	// 模型驱动使用的对象.
	private User user = new User();// 必须手动new
	@Override
	public User getModel() {
		return user;
	}
…
}

// list集合的方式
<input type="text" name="products[0].name" />
// 在Action中的写法，需要提供products的集合，并且提供get和set方法。

// map集合的方式
<input type="text" name="map['one'].name" />
// Action中提供map集合，并且提供get和set方法
```

### 10. 拦截器（重点）

拦截器可以拦截action的方法，不可以拦截jsp的请求，在下一个方法前后（Invocation.invoke()的方法前后）插入一个操作。拦截器就是AOP（Aspect-Oriented Programming）的一种实现。（AOP是指用于在某个方法或字段被访问之前，进行拦截然后在之前或之后加入某些操作。）

#### 10.1 拦截器和过滤器的区别

* 拦截器是基于JAVA反射机制的，而过滤器是基于函数回调的（回调函数是指：A调用B，B又需要调用A中的一个方法）。
* 过滤器依赖于Servlet容器，而拦截器不依赖于Servlet容器。
* 拦截器是拦截对目标action中的某些方法的调用进行拦截，只能对Action请求起作用（Action中的方法）；而过滤器过滤从客户端发送到服务器端的请求，可以对几乎所有的请求起作用（CSS JSP JS）。

> 个人理解：
>
> * 拦截器是执行某个action时，会在其之前或者之后进行某个操作
>
> * 过滤器是检查请求资源或响应资源，对其进行过滤操作

#### 10.2 拦截器链

* 拦截器采用责任链模式
	* 在责任链模式里，很多对象由每一个对象对其下家的引用而连接起来形成一条链。
	* 责任链每一个节点，都可以继续调用下一个节点，也可以阻止流程继续执行。
* 在struts2 中可以定义很多个拦截器，将多个拦截器按照特定顺序 组成拦截器栈 （顺序调用 栈中的每一个拦截器 ）

```java
// 1. 编写拦截器，需要实现Interceptor接口，实现接口中的三个方法
	protected String doIntercept(ActionInvocation invocation) throws Exception {
		// 获取session对象
		User user = (User) ServletActionContext.getRequest().getSession().getAttribute("existUser");
		if(user == null){
			// 说明，没有登录，后面就不会执行了
			return "login";
		}
		return invocation.invoke();
	}

// 2. 需要在struts.xml中进行拦截器的配置，配置一共有两种方式
	// 第一种：定义拦截器
	<interceptors>
		<interceptor name="DemoInterceptor" class="com.itheima.interceptor.DemoInterceptor"/>
	</interceptors>
	
	
	// 第二种：定义拦截器栈
	<interceptors>
		<interceptor name="DemoInterceptor" class="com.itheima.interceptor.DemoInterceptor"/>
		<!-- 定义拦截器栈 -->
		<interceptor-stack name="myStack">
			<interceptor-ref name="DemoInterceptor"/>
			// 默认的拦截器放在最后，防止覆盖掉
			<interceptor-ref name="defaultStack"/>
		</interceptor-stack>
	</interceptors>
	
	<action name="userAction" class="com.itheima.demo3.UserAction">
		<!-- 只要是引用自己的拦截器，默认栈的拦截器就不执行了，必须要手动引入默认栈 
		<interceptor-ref name="DemoInterceptor"/>
		<interceptor-ref name="defaultStack"/>
		-->
		
		<!-- 引入拦截器栈就OK -->
		<interceptor-ref name="myStack"/>
	</action>
```

### 11. ValueStack的概述

#### 11.1 什么是值栈

* 值栈就相当于Struts2框架的数据的中转站，后台向值栈存入一些数据，前台从值栈中获取到数据。
* ValueStack 是 struts2 提供一个接口，实现类 OgnlValueStack ---值栈对象 （OGNL是从值栈中获取数据的 ）
* Action是多例的，有一起请求，创建Action实例，创建一个ActionContext对象，代表的是Action的上下文对象，还会创建一个ValueStack对象。
* 每个Action实例都有一个ValueStack对象 （一个请求 对应 一个ValueStack对象 ）
* 在其中保存当前Action 对象和其他相关对象
* Struts 框架把 ValueStack 对象保存在名为 “struts.valueStack” 的请求属性中,request中 （值栈对象 是 request一个属性）
	* ValueStack vs = (ValueStack)request.getAttribute("struts.valueStack");

#### 11.2 值栈的内部结构 

![值栈的内部结构](.\SSH\值栈的内部结构.bmp)

* 值栈由两部分组成
	> root		-- Struts把动作和相关对象压入 ObjectStack 中--List
	> context  	-- Struts把各种各样的映射关系(一些 Map 类型的对象) 压入 ContextMap 中

* Struts会默认把下面这些映射压入ContextMap（context）中
	* 注意：request代表的是Map集合的key值，value的值其实也是一个Map集合。
	
	> parameters: 该 Map 中包含当前请求的请求参数  ?name=xxx&password=123
	> request: 该 Map 中包含当前 request 对象中的所有属性
	> session: 该 Map 中包含当前 session 对象中的所有属性
	> application:该 Map 中包含当前 application  对象中的所有属性
	> attr: 该 Map 按如下顺序来检索某个属性: request, session, application

* ValueStack中 存在root属性 (CompoundRoot) 、 context 属性 （OgnlContext ）
	> CompoundRoot 就是ArrayList
	> OgnlContext 就是 Map

* context 对应Map 引入 root对象 
	> context中还存在 request、 session、application、 attr、 parameters 对象引用 
	> OGNL表达式访问值栈中的数据
	> * 访问root中数据时 不需要 #
	> * 访问 request、 session、application、 attr、 parameters 对象数据 必须写 # 
	
	> 操作值栈 默认指 操作 root 元素

#### 11.2 ValueStack 和 ActionContext 的关系

* 值栈对象是请求时创建的
* ActionContext是绑定到当前的线程上，那么在每个拦截器或者Action中获取到的ActionContext是同一个。
* ActionContext中存在一个Map集合，该Map集合和ValueStack的context是同一个地址。
* ActionContext中可以获取到ValueStack的引用，以后再开发，使用ActionContext来获取到值栈对象

```
ValueStack vs = ActionContext.getActionContext().getValueStack();
```

#### 11.3 值栈保存数据 （主要针对root栈）

因为主要存放数据是在root属性中，context主要存入一些请求相关的映射关系。

（1）在root保存对象

valueStack.push(Object obj);

- push方法的底层调用root对象的push方法（把元素添加到0位置）

（2）在root中保存map集合

valueStack.set(String key, Object obj);

- 源码获取map集合（map有可能是已经存在的，有可能是新创建的），把map集合push到栈顶，再把数据存入到map集合中。

在jsp中 通过 <s:debug /> 查看值栈的内容

#### 11.4 从值栈中获取值

注意：

* 访问root中数据不需要#；访问context其它对象数据 加 #
* 如果向root中存入对象的话，优先使用push方法；如果向context中存入集合的话，优先要使用set方法。

（1）存入字符串

```
vs.push("美美");
// 获取到栈顶的值
<s:property value="[0].top"/>
```

（2）存入map集合

```
vs.set("msg", "小凤");
<s:property value="[0].top.msg"/>
```

（3）存入对象

```
vs.push(user);
// 栈顶放user对象
<s:property value="[0].top.username"/>
<s:property value="[0].top.password"/>
// [0].top 关键字是可以省略的  findValue()
<s:property value="username"/>
```

（4）存放值为对象的map

```
vs.set("user", user);
<s:property value="[0].top.user.username"/>
<s:property value="[0].top.user.password"/>
// 省略关键字
<s:property value="user.username"/>
```

（5）查询非栈顶的数据

```
User user = new User("小苍","123");
vs.set("user", user);
// [1].top获取ValueStack1Action，[1].top.user返回user对象
<s:property value="[1].top.user.username"/>
```

（6）栈顶是list集合
```
vs.push(ulist);
<s:property value="[0].top[0].username"/>
<s:property value="[0].top[1].username"/>
```

（7）放值为list集合的map

```
vs.set("ulist", ulist);
<s:property value="ulist[0].username"/>
```

（8）迭代的标签
* value要迭代的集合，需要从值栈中获取
* var编写上，把迭代产生的对象默认压入到context栈中，从context栈取值，加#号
* var不编写，默认把迭代产生的对象压入到root栈中	

```
// 8.1 编写var的属性
  <s:iterator value="ulist" var="u">
  <s:property value="#u.username"/>
  <s:property value="#u.password"/>
  </s:iterator>

// 8.2 没有编写var关键字
<s:iterator value="ulist">
	<s:property value="username"/>
	<s:property value="password"/>
</s:iterator>
```

（9）从context栈中获取值，加#号

```
HttpServletRequest request = ServletActionContext.getRequest();
request.setAttribute("msg", "美美");
request.getSession().setAttribute("msg", "小风");

<s:property value="#request.msg"/>
<s:property value="#session.msg"/>
<s:property value="#parameters.id"/>
<s:property value="#attr.msg"/>
```

# Spring学习

## 一、基本概念

### 1. Spring是什么

Spring是一个开源的轻量级框架，相当于service层。

### 2. Spring有什么用

Spring是于2003 年兴起的一个轻量级的Java开发框架，由Rod Johnson在其著作Expert One-On-One J2EE Development and Design中阐述的部分理念和原型衍生而来。

* 它是为了解决企业应用开发的复杂性而创建的。框架的主要优势之一就是其分层架构，分层架构允许使用者选择使用哪一个组件，同时为 J2EE 应用程序开发提供集成的框架。
* Spring使用基本的JavaBean来完成以前只可能由EJB完成的事情。然而，Spring的用途不仅限于服务器端的开发。从简单性、可测试性和松耦合的角度而言，任何Java应用都可以从Spring中受益。
* Spring的核心是控制反转（IoC）和面向切面（AOP）。简单来说，Spring是一个分层的JavaSE/EEfull-stack(一站式) 轻量级开源框架。
* 可以集成其他框架，方便解耦，简化开发。

![Spring的框架](F:\学习笔记\SSH\Spring的框架.png)

## 二、Spring的基本配置

[jar包下载](http://repo.springsource.org/libs-release-local/org/springframework/spring)

### 1. Spring包目录结构

> docs		-- API和开发规范
>
> libs		-- jar包和源码·
>
> schema	-- 约束

### 2. 需要引入的jar包

Spring框架的IOC的功能，那么根据Spring框架的体系结构图能看到，只需要引入如下的jar包：

* Beans
* Core
* Context
* Expression Language

Spring框架也需要引入日志相关的jar包：
* 在spring-framework-3.0.2.RELEASE-dependencies/org.apache.commons/com.springsource.org.apache.commons.logging/1.1.1
* com.springsource.org.apache.commons.logging-1.1.1.jar


还需要引入log4j的jar包：
* spring-framework-3.0.2.RELEASE-dependencies\org.apache.log4j\com.springsource.org.apache.log4j\1.2.15
	
* com.springsource.org.apache.log4j-1.2.15.jar
	

### 3. 编写java类

创建对应的包结构，编写Java的类。

* UserService			-- 接口
* UserServiceImpl		-- 具体的实现类

**注意：**以后使用Spring框架做开发，都需要来编写接口与实现类。

### 4. 编写配置文件

想把UserServiceImpl实现类的创建交给Spring框架来管理，需要创建Spring框架的配置文件，完成配置

* 在src目录下创建applicationContext.xml的配置文件。

* 引入spring的约束，可以在spring-framework-3.2.0.RELEASE\docs\spring-framework-reference\html\xsd-config.html文件中找到具体的约束头信息.
	

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```

* 完成UserService的配置

```
<!-- Spring的快速入门 -->
<bean id="userService" class="com.itcast.demo1.UserServiceImpl"/>
```

### 5. 编写测试程序

采用Spring框架的工厂方式来获取到UserService接口的具体实现类。

```java
public void demo2(){
  	// 使用Spring的工厂:
  	ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
  	// 通过工厂获得类:
  	UserService userService = (UserService) applicationContext.getBean("userService");
  	userService.sayHello();
  }
```

## 三、Spring的深入了解

### 1. bean标签属性

* id属性和name属性的区别：
  * id		-- Bean起个名字，在约束中采用ID的约束，唯一
    * 取值要求：必须以字母开始，可以使用字母、数字、连字符、下划线、句话、冒号	id:不能出现特殊字符

  * name		-- Bean起个名字，没有采用ID的约束（了解）
    * 取值要求：name:出现特殊字符.如果<bean>没有id的话 , name可以当做id使用
    * Spring框架在整合Struts1的框架的时候，Struts1的框架的访问路径是以/开头的，例如：/bookAction

* class属性			-- Bean对象的全路径

* scope属性			-- scope属性代表Bean的作用范围
  * singleton			-- 单例（默认值）
  * prototype			-- 多例，在Spring框架整合Struts2框架的时候，Action类也需要交给Spring做管理，配置把Action类配置成多例！！
  * request			-- 应用在Web项目中，每次HTTP请求都会创建一个新的Bean
  * session			-- 应用在Web项目中，同一个HTTP Session 共享一个Bean
  * globalsession		-- 应用在Web项目中，多服务器间的session，给每个global http session建一个

* Spring初始化bean或销毁bean时，有时需要作一些处理工作，因此spring可以在创建和拆卸bean的时候调用bean的两个生命周期方法，Bean对象的创建和销毁的两个属性配置：
  * init-method		-- 当bean被载入到容器的时候调用init-method属性指定的方法
  * destroy-method	-- 当bean从容器中删除的时候调用destroy-method属性指定的方法
  * 注意：想查看destroy-method的效果，有如下条件
    * scope= singleton有效
    * web容器中会自动调用，但是main函数或测试用例需要手动调用（需要使用ClassPathXmlApplicationContext的close()方法）

### 2. IOC的底层原理

IOC（Inversion of Control）控制反转：指的是bean对象（接口的实现类）的创建权反转(交给)给Spring，作用是实现了程序的解耦合。

![IOC的底层实现原理](.\SSH\IOC的底层实现原理.png)

### 3. DI依赖注入

#### 3.1 依赖注入的概念

DI（Dependency Injection）依赖注入：在Spring框架负责创建Bean对象时，动态的将依赖对象注入到Bean组件中。

```
// 通过依赖注入的功能,可以将需要传入的值写在配置文件中,方便使用
<bean id="us" class="com.itheima.demo1.UserServiceImpl">
	<property name="uname" value="小风"/>
</bean>
```

> DI是基于IOC之上才可以做的

#### 3.2 两种注入方式

（1）构造函数注入

```
// 编写Java的类，提供构造方法
public class Car {
	private String name;
	private double money;
	public Car(String name, double money) {
		this.name = name;
		this.money = money;
	}
	@Override
	public String toString() {
		return "Car [name=" + name + ", money=" + money + "]";
	}
}

// 编写配置文件
<bean id="car" class="com.itheima.demo4.Car">
	<constructor-arg name="name" value="大奔"/>
	<constructor-arg name="money" value="100"/>
</bean>
```

（2）属性setter方法注入

```
// 编写Java的类，提供属性和对应的set方法即可
public class Car {
	private String name;
	private double money;
	public String setName(String name){
		this.name = name;
	}
	public double setMoney(double money){
		this.money = money;
	}
}

// 编写配置文件
<bean id="car" class="com.itheima.demo4.Car">
	<property name="name" value="大奔"/>
	<property name="money" value="100"/>
</bean>
```

#### 3.3 对象和集合属性注入

（1）对象属性注入

<property name="name" rel="具体的Bean的ID或者name的值"/>

```
<bean id="person" class="com.itheima.demo4.Person">
	<property name="pname" value="美美"/>
	<property name="car2" ref="car2"/>
</bean>
```

（2）数组和list集合属性注入

如果是数组或者List集合，注入配置文件的方式是一样的

```
<bean id="collectionBean" class="com.itheima.demo5.CollectionBean">
	<property name="arrs">
		<list>
			<value>美美</value>
			<value>小风</value>
		</list>
	</property>
</bean>
```

（3）set集合属性注入

```
<property name="sets">
	<set>
		<value>哈哈</value>
		<value>呵呵</value>
	</set>
</property>
```

（4）map集合属性注入

```
<property name="map">
    <map>
        <entry key="老王2" value="38"/>
        <entry key="凤姐" value="38"/>
        <entry key="如花" value="29"/>
    </map>
</property>
```

（5）properties属性文件的方式

```
<property name="pro">
	<props>
		<prop key="uname">root</prop>
		<prop key="pass">123</prop>
	</props>
</property>
```

### 4. 注解的方式使用IOC

@Component表示组件(作用在类上)，Spring中提供@Component的三个衍生注解，功能目前来讲是一致的，这三个注解是为了让标注类本身的用途清晰，Spring在后续版本会对其增强：

- @Controller		-- 作用在WEB层
- @Service			-- 作用在业务层
- @Repository		-- 作用在持久层

IOC的注解使用方式：

* 导入注解开发的jar包：引入IOC容器必须的6个jar包；Spring框架的AOP的jar包，spring-aop的jar包。

* 创建对应的包结构，编写Java的类：
  * UserService			-- 接口
  * UserServiceImpl		-- 具体的实现类

* 在src的目录下，创建applicationContext.xml的配置文件，然后引入约束。因为现在想使用注解的方式，那么引入的约束发生了变化，需要引入context的约束，具体的约束如下：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"> <!-- bean definitions here -->
	
</beans>
```

* 在applicationContext.xml配置文件中开启组件扫描：

```
// 1.扫描指定的接口实现类
<context:component-scan base-package="com.itheima.demo1"/>

// 2.扫面整个包
<context:component-scan base-package="com.itheima"/>
```

* 在UserServiceImpl的实现类上添加注解`@Component(value="userService")`，	相当于在XML的配置方式中 `<bean id="userService" class="...">`

* 编写测试代码

```java
public class SpringDemo1 {
	@Test
	public void run1(){
		ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
		UserService us = (UserService) ac.getBean("userService");
		us.save();
	}
}
// 指定id为userService
@Component(value="userService")
public class UserServiceImpl {
	public void save () {
	
	}
}
```

### 5. Bean管理的常用注解

#### 5.1 属性注入的注解

使用注解注入的方式，位于属性之上，可以不用提供set方法


* 如果是注入的普通类型，可以使用value注解
	* @Value			-- 用于注入普通类型

* 如果注入的是对象类型，使用如下注解
	* @Autowired		-- 默认按类型进行自动装配
		* 如果想按名称注入
	*  @Qualifier	-- 强制使用名称注入，当一个接口有多个实现类的时候，使用@Autowired就不知道该注入哪个属性值，需要使用@Qualifier来指定
	* @Resource	-- 相当于@Autowired和@Qualifier一起使用
	  - 强调：Java提供的注解
	  - 属性使用name属性


```
@Autowired("userService")

@Qualifier("userService1")

@Resource(name = "userService")
```

#### 5.2 作用范围注解

注解为@Scope(value="prototype")，作用在类上。值如下：
* singleton		-- 单例，默认值
* prototype		-- 多例

#### 5.3 生命周期的配置（了解）

作用于方法上，相当于指定Bean创建和销毁时执行的方法
* @PostConstruct	-- 相当于init-method
* @PreDestroy		-- 相当于destroy-method

#### 5.4 测试方法的注解

为了简化了JUnit的测试，使用Spring框架也可以整合测试，必须先有JUnit的环境（即已经导入了JUnit4的开发环境）。

* 在程序中引入:spring-test.jar
* 在具体的测试类上添加注解

```java
// 测试使用的JUnit类
@RunWith(SpringJUnit4ClassRunner.class)
// 加载的配置文件
@ContextConfiguration("classpath:applicationContext.xml")
public class SpringDemo1 {
    @Resource(name="userService")
    private UserService userService;
	// 指定为测试方法
    @Test
    public void demo2(){
        userService.save();
    }
}
```

#### 5.5 Bean 的生命周期

[Spring 了解Bean的一生(生命周期)]( https://blog.csdn.net/w_linux/article/details/80086950 )

注意：多例的情况下，实例化好Bean后，Bean的生命周期就不归Spring管了，由用户自己处理。

#### 5.6  Spring实例化Bean时机

* 如果你使用BeanFactory作为Spring Bean的工厂类，则所有的bean都是在第一次使用该Bean的时候实例化 
* 如果你使用ApplicationContext作为Spring Bean的工厂类，则又分为以下几种情况： 
  * 如果bean的scope是singleton的，并且lazy-init为false（默认是false，所以可以不用设置），则ApplicationContext启动的时候就实例化该Bean，并且将实例化的Bean放在一个map结构的缓存中，下次再使用该Bean的时候，直接从这个缓存中取 
  * 如果bean的scope是singleton的，并且lazy-init为true，则该Bean的实例化是在第一次使用该Bean的时候进行实例化 
  * 如果bean的scope是prototype的，则该Bean的实例化是在第一次使用该Bean的时候进行实例化 

### 6. AOP的底层原理

AOP为Aspect Oriented Programming的缩写，意为：面向切面编程。AOP是一种编程范式，隶属于软工范畴，指导开发者如何组织程序结构。AOP最早由AOP联盟的组织提出的，制定了一套规范。Spring将AOP思想引入到框架中，必须遵守AOP联盟的规范。通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

> 个人理解：
>
> 以前增强类的方式是通过继承；现在AOP采取横向抽取机制，取代了传统纵向继承体系重复性代码（性能监视、事务管理、安全检查、缓存），可以在不修改源代码的前提下，对程序进行增强。

#### 6.1 AOP技术底层实现

Srping框架的AOP技术底层也是采用的代理技术，代理的方式提供了两种

* 基于JDK的动态代理：
  * 必须是面向接口的，只有实现了具体接口的类才能生成代理对象

* 基于CGLIB动态代理：
  * 对于没有实现了接口的类，也可以产生代理，产生这个类的子类的方式

（1）使用Proxy类来生成代理对象的一些代码如下：

```
	/**
	 * 使用JDK的方式生成代理对象
	 * @author Administrator
	 */
	public class MyProxyUtils {
		public static UserDao getProxy(final UserDao dao) {
			// 使用Proxy类生成代理对象
			UserDao proxy = (UserDao) Proxy.newProxyInstance(dao.getClass().getClassLoader(),
					dao.getClass().getInterfaces(), new InvocationHandler() {
						
						// 代理对象方法一直线，invoke方法就会执行一次
						public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
							if("save".equals(method.getName())){
								System.out.println("记录日志...");
								// 开启事务
							}
							// 提交事务
							// 让dao类的save或者update方法正常的执行下去
							return method.invoke(dao, args);
						}
					});
			// 返回代理对象
			return proxy;
		}
	}
```

（2）使用cglib动态代理：

```
// 1. 引入CBLIB的开发包
	* 如果想使用CGLIB的技术来生成代理对象，那么需要引入CGLIB的开发的jar包，在Spring框架核心包中已经引入了CGLIB的开发包了。所以直接引入Spring核心开发包即可！

// 2. 编写相关的代码
	public static OrderDaoImpl getProxy(){
		// 创建CGLIB核心的类
		Enhancer enhancer = new Enhancer();
		// 设置父类
		enhancer.setSuperclass(OrderDaoImpl.class);
		// 设置回调函数
		enhancer.setCallback(new MethodInterceptor() {
			@Override
			public Object intercept(Object obj, Method method, Object[] args,
					MethodProxy methodProxy) throws Throwable {
				if("save".equals(method.getName())){
					// 记录日志
					System.out.println("记录日志了...");
				}
				return methodProxy.invokeSuper(obj, args);
			}
		});
		// 生成代理对象
		OrderDaoImpl proxy = (OrderDaoImpl) enhancer.create();
		return proxy;
	}
```

#### 6.2 AOP的相关术语

* Joinpoint(连接点)	-- 所谓连接点是指那些被拦截到的点。在spring中这些点指的是方法，因为spring只支持方法类型的连接点

* Pointcut（切入点）		-- 所谓切入点是指我们要对哪些Joinpoint进行拦截的定义

* Advice(通知/增强)	-- 所谓通知是指拦截到Joinpoint之后所要做的事情就是通知。通知分为前置通知、后置通知、异常通知、最终通知、环绕通知(切面要完成的功能)

* Introduction(引介)	-- 引介是一种特殊的通知在不修改类代码的前提下，Introduction可以在运行期为类动态地添加一些方法或Field

* Target(目标对象)		-- 代理的目标对象

* Weaving(织入)		-- 是指把增强应用到目标对象来创建新的代理对象的过程

* Proxy（代理）		-- 一个类被AOP织入增强后，就产生一个结果代理类

* Aspect(切面)			-- 是切入点和通知的结合，以后来编写和配置的

#### 6.3 使用XML方式进行AOP开发

（1）创建JavaWEB项目，引入具体的开发的jar包
* 先引入Spring框架开发的基本开发包
* 再引入Spring框架的AOP的开发包
	* spring的传统AOP的开发的包
		* spring-aop-4.2.4.RELEASE.jar
		* com.springsource.org.aopalliance-1.0.0.jar
	
	* aspectJ的开发包
		* com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar
		* spring-aspects-4.2.4.RELEASE.jar

（2）创建Spring的配置文件，引入具体的AOP的schema约束

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
```

（3）创建包结构，编写具体的接口和实现类
* com.itheima.demo2
	* CustomerDao			-- 接口
	* CustomerDaoImpl		-- 实现类

（4）将目标类配置到Spring中

```
<bean id="customerDao" class="com.itheima.demo3.CustomerDaoImpl"/>
```

（5）定义切面类

```java
public class MyAspectXml {
	// 定义通知
	public void log(){
		System.out.println("记录日志...");
	}
}

（6）在配置文件中定义切面类

```
<bean id="myAspectXml" class="com.itheima.demo3.MyAspectXml"/>
```

（7）在配置文件中完成aop的配置
```
<aop:config>
	<!-- 引入切面类 -->
	<aop:aspect ref="myAspectXml">
		<!-- 定义通知类型：切面类的方法和切入点的表达式 -->
		<aop:before method="log" pointcut="execution(public * com.itheima.demo3.CustomerDaoImpl.save(..))"/>
	</aop:aspect>
</aop:config>

```

（8）完成测试

​```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class Demo3 {
	@Resource(name="customerDao")
	private CustomerDao customerDao;
	@Test
	public void run1(){
		customerDao.save();
		customerDao.update();
		customerDao.delete();
	}
}
```

#### 6.4 切入点的表达式

在配置切入点的时候，需要定义表达式，切入点表达式的格式如下：execution([修饰符] 返回值类型 包名.类名.方法名(参数))。

注意以下的书写格式：

* 修饰符可以省略不写，不是必须要出现的。
* 返回值类型是不能省略不写的，根据你的方法来编写返回值。可以使用 * 代替。
* 包名例如：com.itheima.demo3.BookDaoImpl
	* 首先com是不能省略不写的，但是可以使用 * 代替
	* 中间的包名可以使用 * 号代替
	* 如果想省略中间的包名可以使用 .. 

* 类名也可以使用 * 号代替，也有类似的写法：*DaoImpl
* 方法也可以使用 * 号代替
* 参数如果是一个参数可以使用 * 号代替，如果想代表任意参数使用 ..

所以按照上面的格式，最精简的格式如下：`execution(public * *(..))`。

#### 6.5 通知类型

* 前置通知
  * 在目标类的方法执行之前执行。
  * 配置文件信息：<aop:before method="before" pointcut-ref="myPointcut3"/>
  * 应用：可以对方法的参数来做校验

* 最终通知
  * 在目标类的方法执行之后执行，如果程序出现了异常，最终通知也会执行。
  * 在配置文件中编写具体的配置：<aop:after method="after" pointcut-ref="myPointcut3"/>
  * 应用：例如像释放资源

* 后置通知
  * 方法正常执行后的通知。		
  * 在配置文件中编写具体的配置：<aop:after-returning method="afterReturning" pointcut-ref="myPointcut2"/>
  * 应用：可以修改方法的返回值

* 异常抛出通知
  * 在抛出异常后通知
  * 在配置文件中编写具体的配置：<aop:after-throwing method="afterThorwing" pointcut-ref="myPointcut3"/>	
  * 应用：包装异常的信息

* 环绕通知
  * 方法的执行前后执行。
  * 在配置文件中编写具体的配置：<aop:around method="around" pointcut-ref="myPointcut2"/>
  * 要注意：目标的方法默认不执行，需要使用ProceedingJoinPoint对来让目标对象的方法执行。

### 7. 注解的方式使用AOP

* 创建JavaWEB项目，引入具体的开发的jar包
  * 先引入Spring框架开发的基本开发包
  * 再引入Spring框架的AOP的开发包
    * spring的传统AOP的开发的包
      * spring-aop-4.2.4.RELEASE.jar
      * com.springsource.org.aopalliance-1.0.0.jar

    * aspectJ的开发包
      * com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar
      * spring-aspects-4.2.4.RELEASE.jar

* 创建Spring的配置文件，引入具体的AOP的schema约束

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
	
</beans>
```

* 创建包结构，编写具体的接口和实现类
  * com.itheima.demo1
    * CustomerDao			-- 接口
    * CustomerDaoImpl		-- 实现类

* 将目标类配置到Spring中

```
<bean id="customerDao" class="com.itheima.demo1.CustomerDaoImpl"/>
```

* 定义切面类
  * 添加切面和通知的注解
    * @Aspect					-- 定义切面类的注解

    * 通知类型（注解的参数是切入点的表达式）
      * @Before				-- 前置通知
      * @AfterReturing		-- 后置通知
      * @Around				-- 环绕通知
      * @After				-- 最终通知
      * @AfterThrowing		-- 异常抛出通知

```java
@Aspect
public class MyAspectAnno {
	@Before(value="execution(public void com.itheima.demo1.CustomerDaoImpl.save())")
	public void log(){
		System.out.println("记录日志...");
	}
}
```

* 在配置文件中定义切面类

```xml
<bean id="myAspectAnno" class="com.itheima.demo1.MyAspectAnno"/>
```

* 在配置文件中开启自动代理

```
<aop:aspectj-autoproxy/>
```

* 完成测试

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class Demo1 {
    @Resource(name="customerDao")
    private CustomerDao customerDao;

    @Test
    public void run1(){
        customerDao.save();
        customerDao.update();
    }
}
```
[Spring中使用AOP的两种方式（基于注解和XML配置）]( https://blog.csdn.net/Luck_ZZ/article/details/100047014 )

### 8. Spring框架的JDBC模板技术

Spring框架中提供了很多持久层的模板类来简化编程，使用模板类编写程序会变的简单

* 提供了JDBC模板，Spring框架提供的
  * JdbcTemplate类

* Spring框架可以整合Hibernate框架，也提供了模板类
  * HibernateTemplate类

#### 8.1 JDBC模板类使用

* 创建数据库的表结构

```mysql
create database spring_day03;
use spring_day03;
create table t_account(
	id int primary key auto_increment,
	name varchar(20),
	money double
);
```

* 引入开发的jar包
  * 先引入IOC基本的6个jar包
  * 再引入Spring-aop的jar包
  * 最后引入JDBC模板需要的jar包
    * MySQL数据库的驱动包
    * Spring-jdbc.jar
    * Spring-tx.jar

* 编写测试代码（自己来new对象的方式）

```java
@Test
public void run1(){
	// 创建连接池，先使用Spring框架内置的连接池
	DriverManagerDataSource dataSource = new DriverManagerDataSource();
	dataSource.setDriverClassName("com.mysql.jdbc.Driver");
	dataSource.setUrl("jdbc:mysql:///spring_day03");
	dataSource.setUsername("root");
	dataSource.setPassword("root");
	// 创建模板类
	JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
	// 完成数据的添加
	jdbcTemplate.update("insert into t_account values (null,?,?)", "测试",10000);
}
```

#### 8.2 Spring框架来管理模板类

刚才编写的代码使用的是new的方式，应该把这些类交给Spring框架来管理，修改的步骤如下：

* Spring管理内置的连接池

```xml
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
  	<property name="driverClassName" value="com.mysql.jdbc.Driver"/>
  	<property name="url" value="jdbc:mysql:///spring_day03"/>
  	<property name="username" value="root"/>
  	<property name="password" value="root"/>
  </bean>
```

* Spring管理模板类

```xml
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
  	<property name="dataSource" ref="dataSource"/>
  </bean>
```

* 编写测试程序

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class Demo2 {
    @Resource(name="jdbcTemplate")
    private JdbcTemplate jdbcTemplate;

    @Test
    public void run2(){
        jdbcTemplate.update("insert into t_account values (null,?,?)", "测试2",10000);
    }
}
```

#### 8.3 Spring框架管理开源的连接池

（1）管理DBCP连接池：
* 先引入DBCP的2个jar包
	* com.springsource.org.apache.commons.dbcp-1.2.2.osgi.jar
	* com.springsource.org.apache.commons.pool-1.5.3.jar
* 编写配置文件

```xml
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
  	<property name="driverClassName" value="com.mysql.jdbc.Driver"/>
  	<property name="url" value="jdbc:mysql:///spring_day03"/>
  	<property name="username" value="root"/>
  	<property name="password" value="root"/>
  </bean>
```

（2）管理C3P0连接池
* 先引入C3P0的jar包
	* com.springsource.com.mchange.v2.c3p0-0.9.1.2.jar
* 编写配置文件

```xml
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
  	<property name="driverClass" value="com.mysql.jdbc.Driver"/>
  	<property name="jdbcUrl" value="jdbc:mysql:///spring_day03"/>
  	<property name="user" value="root"/>
  	<property name="password" value="root"/>
  </bean>
```

#### 8.4 JDBC模板的简单操作

```java
1. 增删改查的操作
	@RunWith(SpringJUnit4ClassRunner.class)
	@ContextConfiguration("classpath:applicationContext.xml")
	public class SpringDemo3 {
		
		@Resource(name="jdbcTemplate")
		private JdbcTemplate jdbcTemplate;
		
		@Test
		// 插入操作
		public void demo1(){
			jdbcTemplate.update("insert into account values (null,?,?)", "冠希",10000d);
		}
		
		@Test
		// 修改操作
		public void demo2(){
			jdbcTemplate.update("update account set name=?,money =? where id = ?", "思雨",10000d,5);
		}
		
		@Test
		// 删除操作
		public void demo3(){
			jdbcTemplate.update("delete from account where id = ?", 5);
		}
		
		@Test
		// 查询一条记录
		public void demo4(){
			Account account = jdbcTemplate.queryForObject("select * from account where id = ?", new BeanMapper(), 1);
			System.out.println(account);
		}
		
		@Test
		// 查询所有记录
		public void demo5(){
			List<Account> list = jdbcTemplate.query("select * from t_account", new BeanMapper());
			for (Account account : list) {
				System.out.println(account);
			}
		}
	}
	
	class BeanMapper implements RowMapper<Account>{
		public Account mapRow(ResultSet rs, int arg1) throws SQLException {
			Account account = new Account();
			account.setId(rs.getInt("id"));
			account.setName(rs.getString("name"));
			account.setMoney(rs.getDouble("money"));
			return account;
		}
	}
```

### 9. Spring框架的事务管理

事务：指的是逻辑上一组操作，组成这个事务的各个执行单元，要么一起成功，要么一起失败。

#### 9.1 事务的概念

（1）事务的特性
* 原子性
* 一致性
* 隔离性

* 持久性

（2）如果不考虑隔离性,引发安全性问题
* 读问题:
	* 脏读:
	* 不可重复读:
	* 虚读:

* 写问题:
	* 丢失更新:

（3）如何解决安全性问题
* 读问题解决，设置数据库隔离级别
* 写问题解决可以使用 悲观锁和乐观锁的方式解决

#### 9.2 Spring框架的事务管理相关的类

* PlatformTransactionManager接口		-- 平台事务管理器.(真正管理事务的类)。该接口有具体的实现类，根据不同的持久层框架，需要选择不同的实现类。PlatformTransactionManager接口中实现类和常用的方法：

  * 接口的实现类
    * 如果使用的Spring的JDBC模板或者MyBatis框架，需要选择DataSourceTransactionManager实现类
    * 如果使用的是Hibernate的框架，需要选择HibernateTransactionManager实现类

  * 该接口的常用方法
    * void commit(TransactionStatus status) 
    * TransactionStatus getTransaction(TransactionDefinition definition) 
    * void rollback(TransactionStatus status) 

* TransactionDefinition接口			-- 事务定义信息（事务的隔离级别、传播行为、超时、只读）
  * 事务隔离级别的常量
    * static int ISOLATION_DEFAULT 					-- 采用数据库的默认隔离级别
    * static int ISOLATION_READ_UNCOMMITTED 
    * static int ISOLATION_READ_COMMITTED 
    * static int ISOLATION_REPEATABLE_READ 
    * static int ISOLATION_SERIALIZABLE 
  * 事务的传播行为常量（不用设置，使用默认值），先解释什么是事务的传播行为：解决的是业务层之间的方法调用。
    * PROPAGATION_REQUIRED（默认值）	-- A中有事务，使用A中的事务。如果没有，B就会开启一个新的事务，将A包含进来（保证A、B在同一个事务中），默认值。
    * PROPAGATION_SUPPORTS			-- A中有事务，使用A中的事务。如果A中没有事务，那么B也不使用事务。
    * PROPAGATION_MANDATORY			-- A中有事务，使用A中的事务。如果A没有事务，抛出异常。
    * PROPAGATION_REQUIRES_NEW（记）-- A中有事务，将A中的事务挂起，B创建一个新的事务（保证A,B没有在一个事务中）。
    * PROPAGATION_NOT_SUPPORTED		-- A中有事务，将A中的事务挂起。
    * PROPAGATION_NEVER 			-- A中有事务，抛出异常。
    * PROPAGATION_NESTED（记）		-- 嵌套事务，当A执行之后，就会在这个位置设置一个保存点，如果B没有问题，执行通过；如果B出现异常，运行客户根据需求回滚（选择回滚到保存点或者是最初始状态）。
* TransactionStatus接口				-- 事务的状态

> 上述对象之间的关系：
>
> PlatformTransactionManager平台事务管理器真正管理事务对象，根据事务定义的信息TransactionDefinition 进行事务管理，在管理事务中产生一些状态，将状态记录到TransactionStatus中。

#### 9.3 Spring框架的编程式的事务管理

Spring为了简化事务管理的代码:提供了模板类 TransactionTemplate，所以手动编程的方式来管理事务，只需要使用该模板类即可。手动编程方式的具体步骤如下：

* 配置一个事务管理器，Spring使用PlatformTransactionManager接口来管理事务，所以咱们需要使用到他的实现类！！

```xml
<!-- 配置事务管理器 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"/>
</bean>
```

* 配置事务管理的模板

```xml
<!-- 配置事务管理的模板 -->
<bean id="transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate">
	<property name="transactionManager" ref="transactionManager"/>
</bean>
```

* 在需要进行事务管理的类中,注入事务管理的模板

```xml
<bean id="accountService" class="com.itheima.demo1.AccountServiceImpl">
	<property name="accountDao" ref="accountDao"/>
	<property name="transactionTemplate" ref="transactionTemplate"/>
</bean>
```

* 在业务层使用模板管理事务

```java
* // 注入事务模板对象
private TransactionTemplate transactionTemplate;
public void setTransactionTemplate(TransactionTemplate transactionTemplate) {
	this.transactionTemplate = transactionTemplate;
}

public void pay(final String out, final String in, final double money) {
    transactionTemplate.execute(new TransactionCallbackWithoutResult() {
        protected void doInTransactionWithoutResult(TransactionStatus status) {
            // 扣钱
            accountDao.outMoney(out, money);
            int a = 10/0;
            // 加钱
            accountDao.inMoney(in, money);
        }
    });
}
```
#### 9.4 Spring框架的事务管理之基于AspectJ的XML方式

* 引入AOP的开发包

* 配置事务管理器

```xml
<!-- 配置事务管理器 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"/>
</bean>
```

* 配置事务增强

```xml
<!-- 配置事务增强 -->
<tx:advice id="myAdvice" transaction-manager="transactionManager">
	<tx:attributes>
		<!--
			name		：绑定事务的方法名，可以使用通配符，可以配置多个。
			propagation	：传播行为
			isolation	：隔离级别
			read-only	：是否只读
			timeout		：超时信息
			rollback-for：发生哪些异常回滚.
			no-rollback-for：发生哪些异常不回滚.
		 -->
		<!-- 哪些方法加事务 -->
		<tx:method name="pay" propagation="REQUIRED"/>
	</tx:attributes>
</tx:advice>
```

* 配置AOP的切面
  * 注意：如果是自己编写的切面，使用`<aop:aspect>`标签，如果是系统制作的，使用`<aop:advisor>`标签。

```xml
<!-- 配置AOP切面产生代理 -->
<aop:config>
 	<aop:advisor advice-ref="myAdvice" pointcut="execution(* com.itheima.demo2.AccountServiceImpl.pay(..))"/>
 </aop:config>
```

* 编写测试类

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext2.xml")
public class Demo2 {
    @Resource(name="accountService")
    private AccountService accountService;

    @Test
    public void run1(){
        accountService.pay("冠希", "美美", 1000);
    }
}
```
 [<aop:aspect>与<aop:advisor>的区别](https://blog.csdn.net/u011983531/article/details/70504281 )

> 个人理解：
>
> `<aop:advisor>`：定义通知器，若配置事务增强，是针对切入点PointCut进行事务方面的增强，给这些方法增加事务的特性，而不是要在切入点之前或之后增加什么操作；如果不使用事务增强，则需要实现通知接口，使用方法和`<aop:aspect>`一样，也可以在切入点前后进行某些增强操作。

#### 9.5 Spring框架的事务管理之基于AspectJ的注解方式

* 配置事务管理器

```xml
<!-- 配置事务管理器  -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"/>
</bean>
```

* 开启注解事务

```xml
<!-- 开启注解事务 -->
<tx:annotation-driven transaction-manager="transactionManager"/>
```

* 在业务层上添加一个注解：@Transactional（可以被用在类或方法上，可以指定属性值，也可以不写使用默认值）

* 编写测试类	

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext3.xml")
public class Demo3 {
    @Resource(name="accountService")
    private AccountService accountService;

    @Test
    public void run1(){
        accountService.pay("冠希", "美美", 1000);
    }
}
```
[Spring事务管理之几种方式实现事务]( https://blog.csdn.net/chinacr07/article/details/78817449 )

> 事务的注意事项：
>
> 在默认的代理模式下，只有目标方法由外部调用，才可以被Spring的事务拦截器拦截，在同一个类中的两个方法直接调用，是不会被拦截的。可以使用AspectJ取代Spring AOP代理解决，也可以使用外部的方法来调用，不过要对两个方法增加事务的注解`@Transactional(propagation=Propagation.REQUIRES_NEW)`，表示暂停当前事务，另创一个事务 。 

### 10. Spring整合Hibernate延迟加载的问题

延迟加载的时候，在WEB层查询对象的时候程序会抛出异常。

因为这里采用的是getHibernateTemplate()，session是由Spring注入管理的。延迟加载还没有发生SQL语句，而此时session已经被Spring关闭，查询到的JavaBean对象已经变成了托管态对象，所以无法加载显示。 

解决方法有以下两个：

（1）取消延迟加载

```
lazy = false
```

（2）使用Spring提供的过滤器

 该filter会将session绑定至当前请求的线程上，这样只要是在当前请求的生命周期内，可以随时访问session。 

```
		<filter>
			<filter-name>OpenSessionInViewFilter</filter-name>
			<filter-class>org.springframework.orm.hibernate5.support.OpenSessionInViewFilter</filter-class>
		</filter>
		<filter-mapping>
			<filter-name>OpenSessionInViewFilter</filter-name>
			<url-pattern>/*</url-pattern>
		</filter-mapping>
```

注意：需要在struts2的核心过滤器之前进行配置。

