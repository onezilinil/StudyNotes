# MyBatis学习

## 一、基本概念

### 1. Mybatis是什么

MyBatis 本是apache的一个开源项目iBatis, 2010年这个项目由apache software foundation 迁移到了google code，并且改名为MyBatis 。2013年11月迁移到Github。

MyBatis是一个优秀的持久层框架，它对jdbc的操作数据库的过程进行封装，使开发者只需要关注 SQL 本身，而不需要花费精力去处理例如注册驱动、创建connection、创建statement、手动设置参数、结果集检索等jdbc繁杂的过程代码。

Mybatis通过xml或注解的方式将要执行的各种statement（statement、preparedStatement、CallableStatement）配置起来，并通过java对象和statement中的sql进行映射生成最终执行的sql语句，最后由mybatis框架执行sql并将结果映射成java对象并返回。

## 二、Mybatis的基本配置

### 1. 传统的jdbc方式开发

* 加载数据库驱动

* 创建并获取数据库链接

* 创建jdbc statement对象

* 设置sql语句

* 设置sql语句中的参数(使用preparedStatement)

* 通过statement执行sql并获取结果

* 对sql执行结果进行解析处理

* 释放资源(resultSet、preparedstatement、connection)

```java
public static void main(String[] args) {
			Connection connection = null;
			PreparedStatement preparedStatement = null;
			ResultSet resultSet = null;
			
			try {
				//加载数据库驱动
				Class.forName("com.mysql.jdbc.Driver");
				
				//通过驱动管理类获取数据库链接
				connection =  DriverManager.getConnection("jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8", "root", "root");
				//定义sql语句 ?表示占位符
			String sql = "select * from user where username = ?";
				//获取预处理statement
				preparedStatement = connection.prepareStatement(sql);
				//设置参数，第一个参数为sql语句中参数的序号（从1开始），第二个参数为设置的参数值
				preparedStatement.setString(1, "王五");
				//向数据库发出sql执行查询，查询出结果集
				resultSet =  preparedStatement.executeQuery();
				//遍历查询结果集
				while(resultSet.next()){
					System.out.println(resultSet.getString("id")+"  "+resultSet.getString("username"));
				}
			} catch (Exception e) {
				e.printStackTrace();
			}finally{
				//释放资源
				if(resultSet!=null){
					try {
						resultSet.close();
					} catch (SQLException e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
				}
				if(preparedStatement!=null){
					try {
						preparedStatement.close();
					} catch (SQLException e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
				}
				if(connection!=null){
					try {
						connection.close();
					} catch (SQLException e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
				}

			}

		}
```

> 传统的方式缺点：
>
> * jdbc连接过程代码太多，而且传入参数的方式太复杂，接受到返回参数还要对返回参数进行处理。
> * 数据库链接创建、释放频繁造成系统资源浪费从而影响系统性能，如果使用数据库链接池可解决此问题
> * Sql语句在代码中硬编码，造成代码不易维护，实际应用sql变化的可能较大，sql变动需要改变java代码。
> * 使用preparedStatement向占有位符号传参数存在硬编码，因为sql语句的where条件不一定，可能多也可能少，修改sql还要修改代码，系统不易维护。
> * 对结果集解析存在硬编码（查询列名），sql变化导致解析代码变化，系统不易维护，如果能将数据库记录封装成pojo对象解析比较方便。

### 2. Mybatis配置文件流程

![Mybatis配置的基本流程](F:\学习笔记\SSM\Mybatis配置的基本流程.png)

Mybatis配置：

* SqlMapConfig.xml，此文件作为mybatis的全局配置文件，配置了mybatis的运行环境等信息。

* mapper.xml文件即sql映射文件，文件中配置了操作数据库的sql语句。此文件需要在SqlMapConfig.xml中加载。

执行过程：

* 通过mybatis环境等配置信息构造SqlSessionFactory即会话工厂

* 由会话工厂创建sqlSession即会话，操作数据库需要通过sqlSession进行。

* mybatis底层自定义了Executor执行器接口操作数据库，Executor接口有两个实现，一个是基本执行器、一个是缓存执行器。

* Mapped Statement也是mybatis一个底层封装对象，它包装了mybatis配置信息及sql映射信息等。mapper.xml文件中一个sql对应一个Mapped Statement对象，sql的id即是Mapped statement的id。

* Mapped Statement对sql执行输入参数进行定义，包括HashMap、基本类型、pojo，Executor通过Mapped Statement在执行sql前将输入的java对象映射至sql中，输入参数映射就是jdbc编程中对preparedStatement设置参数。

* Mapped Statement对sql执行输出结果进行定义，包括HashMap、基本类型、pojo，Executor通过Mapped Statement在执行sql后将输出结果映射至java对象中，输出结果映射过程相当于jdbc编程中对结果的解析处理过程。

### 3. 配置文件

加入mybatis核心包、依赖包、数据驱动包。

mybatis默认使用log4j作为输出日志信息，创建log4j.properties：

```properties
# Global logging configuration
log4j.rootLogger=DEBUG, stdout
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

SqlMapConfig.xml是mybatis核心配置文件，配置内容为数据源、事务管理。创建SqlMapConfig.xml：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 和spring整合后 environments配置将废除-->
	<environments default="development">
		<environment id="development">
		<!-- 使用jdbc事务管理-->
			<transactionManager type="JDBC" />
		<!-- 数据库连接池-->
			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.jdbc.Driver" />
				<property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8" />
				<property name="username" value="root" />
				<property name="password" value="root" />
			</dataSource>
		</environment>
	</environments>
	<!-- 加载sql映射文件-->
    <mappers>
        <mapper resource="sqlmap/User.xml"/>
    </mappers>
</configuration>
```

user.xml是pojo类的映射文件，通常以pojo类的名字为文件名。

```xml
<mapper namespace="test" >
  <resultMap id="BaseResultMap" type="cn.itheima.pojo.Orders" >
    <id column="id" property="id" jdbcType="INTEGER" />
    <result column="user_id" property="userId" jdbcType="INTEGER" />
    <result column="number" property="number" jdbcType="VARCHAR" />
    <result column="createtime" property="createtime" jdbcType="TIMESTAMP" />
    <result column="note" property="note" jdbcType="VARCHAR" />
  </resultMap>
  <!--
    parameterType：定义输入到sql中的映射类型，
    resultType：定义结果映射类型。
  -->
  <select id="findUserByUsername" parameterType="java.lang.String" 
			resultType="cn.itcast.mybatis.po.User">
	   select * from user where username like '%${value}%' 
	</select>
</mapper>
```

### 4. 测试程序

```java
public class Mybatis_first {
	
	//会话工厂
	private SqlSessionFactory sqlSessionFactory;

	@Before
	public void createSqlSessionFactory() throws IOException {
		// 配置文件
		String resource = "SqlMapConfig.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);

		// 使用SqlSessionFactoryBuilder从xml配置文件中创建SqlSessionFactory
		sqlSessionFactory = new SqlSessionFactoryBuilder()
				.build(inputStream);

	}

	// 根据 id查询用户信息
	@Test
	public void testFindUserByName() {
		// 数据库会话实例
		SqlSession sqlSession = null;
		try {
			// 创建数据库会话实例sqlSession
			sqlSession = sqlSessionFactory.openSession();
			// 查询单个记录，根据用户id查询用户信息，这里的test与配置文件namespace对应
			List<User> list = sqlSession.selectList("test.findUserByUsername", "张");
			System.out.println(list.size());
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			if (sqlSession != null) {
				sqlSession.close();
			}
		}

	}
}
```

> #{} 和 ${} 的区别：
>
> * #{}表示使用preparedstatement设置占位符并将变量值传到sql上，会自自动进行java类型和jdbc类型的转换，并会在两边加上`""`，变成字符串。
>
> * ${}表示 使用参数与${}替换，做字符串的拼接，不会进行类型转换，不会加上`""`，会导致sql注入的问题。

## 三、Mybatis的深入了解

### 1、主键返回

（1）主键自增返回

通过修改sql映射文件，可以将mysql自增主键返回：

```xml
<insert id="insertUser" parameterType="cn.itcast.mybatis.po.User"*>
    <!-- selectKey将主键返回，需要再返回 -->
    <selectKey keyProperty="id" order="AFTER" resultType="java.lang.Integer">
        select LAST_INSERT_ID()
    </selectKey>
    insert into user(username,birthday,sex,address)
    values(#{username},#{birthday},#{sex},#{address});
</insert>
```

* 添加selectKey实现将主键返回

* keyProperty：返回的主键存储在pojo中的哪个属性

* order：selectKey的执行顺序，是相对与insert语句来说，由于mysql的自增原理执行完insert语句之后才将主键生成，所以这里selectKey的执行顺序为after

* resultType:返回的主键是什么类型

* LAST_INSERT_ID()：是mysql的函数，返回auto_increment自增列新记录id值。

（2）uuid的主键返回

```xml
<insert  id="insertUser" parameterType="cn.itcast.mybatis.po.User">
    <selectKey resultType="java.lang.String" order="BEFORE" keyProperty="id">
        select uuid()
    </selectKey>
    insert into user(id,username,birthday,sex,address) 
    values(#{id},#{username},#{birthday},#{sex},#{address})
</insert>
```

* 需要增加通过select uuid()得到uuid值

注意：这里使用的order是“BEFORE”，表示在insert之前就指定了主键。

### 2. dao的开发方法

使用Mybatis开发Dao，通常有两个方法，即原始Dao开发方法和Mapper接口开发方法。

#### 2.1 原始的dao开发方式

（1）编写配置文件：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="test">
<!-- 根据id获取用户信息 -->
	<select id="findUserById" parameterType="int" resultType="cn.itcast.mybatis.po.User">
		select * from user where id = #{id}
	</select>
<!-- 添加用户 -->
	<insert id="insertUser" parameterType="cn.itcast.mybatis.po.User">
	<selectKey keyProperty="id" order="AFTER" resultType="java.lang.Integer">
		select LAST_INSERT_ID() 
	</selectKey>
	  insert into user(username,birthday,sex,address) 
	  values(#{username},#{birthday},#{sex},#{address})
	</insert>
</mapper>
```

（2）dao接口的编写

```java
Public interface UserDao {
	public User getUserById(int id) throws Exception;
	public void insertUser(User user) throws Exception;
}

Public class UserDaoImpl implements UserDao {
	
	//注入SqlSessionFactory
	public UserDaoImpl(SqlSessionFactory sqlSessionFactory){
		this.setSqlSessionFactory(sqlSessionFactory);
	}
	
	private SqlSessionFactory sqlSessionFactory;
	@Override
	public User getUserById(int id) throws Exception {
		SqlSession session = sqlSessionFactory.openSession();
		User user = null;
		try {
			//通过sqlsession调用selectOne方法获取一条结果集
			//参数1：指定定义的statement的id,参数2：指定向statement中传递的参数
			user = session.selectOne("test.findUserById", 1);
			System.out.println(user);
						
		} finally{
			session.close();
		}
		return user;
	}
	
	@Override
	Public void insertUser(User user) throws Exception {
		SqlSession sqlSession = sqlSessionFactory.openSession();
		try {
			sqlSession.insert("insertUser", user);
			sqlSession.commit();
		} finally{
			session.close();
		}
	}
}
```

（3）测试

```java
private SqlSessionFactory sqlSessionFactory;
	
	@Before
	public void init() throws Exception {
		SqlSessionFactoryBuilder sessionFactoryBuilder = new SqlSessionFactoryBuilder();
		InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
		sqlSessionFactory = sessionFactoryBuilder.build(inputStream);
	}

	@Test
	public void testGetUserById() {
		UserDao userDao = new UserDaoImpl(sqlSessionFactory);
		User user = userDao.getUserById(22);
		System.out.println(user);
	}
}
```

> 可以看出：
>
> 需要创建会话工厂，以及创建会话，并且需要指定namespace的值以及sql标签的id值，这样的话仍然需要编写大量的代码，并且不易于扩展。

#### 2.2 Mapper动态代理方式   

Mapper接口开发方法只需要程序员编写Mapper接口（相当于Dao接口），由Mybatis框架根据接口定义创建接口的动态代理对象，代理对象的方法体同上边Dao接口实现类方法。 

（1）配置文件

* 修改namespace的值为 UserMapper 接口路径。将UserMapper.xml放在classpath 下mapper目录下。

* sql标签的id值需要与dao接口的方法名相同
* parameterType的值需要和方法传入参数的类型相同，resultType需要和方法返回值类型相同

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.itcast.mybatis.mapper.UserMapper">
<!-- 根据id获取用户信息 -->
	<select id="findUserById" parameterType="int" resultType="cn.itcast.mybatis.po.User">
		select * from user where id = #{id}
	</select>
<!-- 自定义条件查询用户列表 -->
	<select id="findUserByUsername" parameterType="java.lang.String" 
			resultType="cn.itcast.mybatis.po.User">
	   select * from user where username like '%${value}%' 
	</select>
<!-- 添加用户 -->
	<insert id="insertUser" parameterType="cn.itcast.mybatis.po.User">
	<selectKey keyProperty="id" order="AFTER" resultType="java.lang.Integer">
		select LAST_INSERT_ID() 
	</selectKey>
	  insert into user(username,birthday,sex,address) 
	  values(#{username},#{birthday},#{sex},#{address})
	</insert>

</mapper>
```

（2）接口文件

```java
Public interface UserMapper {
	//根据用户id查询用户信息
	public User findUserById(int id) throws Exception;
	//查询用户列表
	public List<User> findUserByUsername(String username) throws Exception;
	//添加用户信息
	public void insertUser(User user)throws Exception; 
}
```

（3）测试

```java
Public class UserMapperTest extends TestCase {

	private SqlSessionFactory sqlSessionFactory;
	@Before
	protected void setUp() throws Exception {
		//mybatis配置文件
		String resource = "sqlMapConfig.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		//使用SqlSessionFactoryBuilder创建sessionFactory
		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	}
	@Test
	Public void testFindUserById() throws Exception {
		//获取session
		SqlSession session = sqlSessionFactory.openSession();
		//获取mapper接口的代理对象
		UserMapper userMapper = session.getMapper(UserMapper.class);
		//调用代理对象方法
		User user = userMapper.findUserById(1);
		System.out.println(user);
		//关闭session
		session.close();
		
	}
	@Test
	public void testFindUserByUsername() throws Exception {
		SqlSession sqlSession = sqlSessionFactory.openSession();
		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
		List<User> list = userMapper.findUserByUsername("张");
		System.out.println(list.size());

	}
    @Test
	Public void testInsertUser() throws Exception {
		//获取session
		SqlSession session = sqlSessionFactory.openSession();
		//获取mapper接口的代理对象
		UserMapper userMapper = session.getMapper(UserMapper.class);
		//要添加的数据
		User user = new User();
		user.setUsername("张三");
		user.setBirthday(new Date());
		user.setSex("1");
		user.setAddress("北京市");
		//通过mapper接口添加用户
		userMapper.insertUser(user);
		//提交
		session.commit();
		//关闭session
		session.close();
	}
}
```

### 3. Mybatis中几种主要的类

#### 3.1 SqlSessionFactoryBuilder

SqlSessionFactoryBuilder用于加载配置文件以及创建SqlSessionFactory，SqlSessionFacoty一旦创建完成就不需要SqlSessionFactoryBuilder了，因为SqlSession是通过SqlSessionFactory生产，所以可以将SqlSessionFactoryBuilder当成一个工具类使用，最佳使用范围是方法范围即方法体内局部变量。

```java
String resource = "SqlMapConfig.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);

		// 使用SqlSessionFactoryBuilder从xml配置文件中创建SqlSessionFactory
		sqlSessionFactory = new SqlSessionFactoryBuilder()
				.build(inputStream);
```

#### 3.2 SqlSessionFactory

 SqlSessionFactory是一个接口，接口中定义了openSession的不同重载方法，SqlSessionFactory的最佳使用范围是整个应用运行期间，一旦创建后可以重复使用，通常以单例模式管理SqlSessionFactory。

#### 3.3 SqlSession

SqlSession是一个面向用户的接口， sqlSession中定义了数据库操作方法，如：查询、插入、更新、删除等。每个线程都应该有它自己的SqlSession实例，SqlSession的实例不能共享使用，它也是线程不安全的。因此最佳的范围是请求或方法范围，绝对不能将SqlSession实例的引用放在一个类的静态字段或实例字段中。

```java
SqlSession session = sqlSessionFactory.openSession();
try {
 // do work
} finally {
    // 需要关闭
	session.close();
}
```

### 4. SqlMapConfig.XML配置文件详解

#### 4.1 引入外部的配置文件

* 先创建一个db.properties文件放置连接信息

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8
jdbc.username=root
jdbc.password=root
```

* 在SqlMapConfig.xml文件中引入

```xml
// 加载配置文件
<properties resource="db.properties"/>
<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="${jdbc.driver}"/>
            <property name="url" value="${jdbc.url}"/>
            <property name="username" value="${jdbc.username}"/>
            <property name="password" value="${jdbc.password}"/>
        </dataSource>
    </environment>
</environments>
```

注意： MyBatis 将按照下面的顺序来加载属性：

* 在 properties 元素体内定义的属性首先被读取。 

* 然后会读取properties 元素中resource或 url 加载的属性，它会覆盖已读取的同名属性。 

#### 4.2 自定义别名

在SqlMapConfig.xml中配置：

```xml
<typeAliases>
	<!-- 1.单个别名定义 -->
	<typeAlias alias="user" type="cn.itcast.mybatis.po.User"/>
	<!-- 2.批量别名定义，扫描整个包下的类，别名为类名（首字母大写或小写都可以） -->
	<package name="cn.itcast.mybatis.po"/>
</typeAliases>
```

在映射文件中：

```xml
<!--原本要写这么长-->
<insert id="addUser2" parameterType="com.dto.User">
    insert into users(name,age) values(#{name},#{age})
</insert>

<!--现在只要写指定的别名就可以-->
<insert id="addUser2" parameterType="User">
    insert into users(name,age) values(#{name},#{age})
</insert>
```

#### 4.3 resultMap

resultType可以指定pojo将查询结果映射为pojo，但需要pojo的属性名和sql查询的列名一致方可映射成功。如果sql查询字段名和pojo的属性名不一致，可以通过resultMap将字段名和属性名作一个对应关系 ，resultMap实质上还需要将查询结果映射到pojo对象中。

resultMap可以实现将查询结果映射为复杂类型的pojo，比如在查询结果映射对象中包括pojo和list实现一对一查询和一对多查询。

```xml
通过<resultMap>映射实体类属性名和表的字段名一一对应关系 -->
    <select id="selectOrderResultMap" parameterType="int" resultMap="orderResultMap">
        select * from orders where order_id=#{id}
    </select>
    <!--通过<resultMap>映射实体类属性名和表的字段名对应关系 -->
    <resultMap type="me.gacl.domain.Order" id="orderResultMap">
        <!-- 用id属性来映射主键字段 -->
        <id property="id" column="order_id"/>
        <!-- 用result属性来映射非主键字段 -->
        <result property="orderNo" column="order_no"/>
        <result property="price" column="order_price"/>
    </resultMap>
```

#### 4.4 mappers（映射器）

Mapper配置的几种方法：

（1）`<mapper resource=" " />`

使用相对于类路径的资源

```
<mapper resource="sqlmap/User.xml" />
```

（2）`<mapper class=" " />`

使用mapper接口类路径

```
<mapper class="cn.itcast.mybatis.mapper.UserMapper"/>
```

注意：此种方法要求mapper接口名称和mapper映射文件名称相同，且放在同一个目录中。

 （3）`<package name=""/>`

注册指定包下的所有mapper接口

```
<package name="cn.itcast.mybatis.mapper"/>
```

注意：此种方法要求mapper接口名称和mapper映射文件名称相同，且放在同一个目录中。

### 5.动态sql

通过mybatis提供的各种标签方法实现动态拼接sql。

#### 5.1 `<if>`

```XML
<!-- 传递pojo综合查询用户信息 -->
	<select id="findUserList" parameterType="user" resultType="user">
		select * from user 
		where 1=1 
        // 直接使用传入参数的属性进行判断
		<if test="id!=null and id!=''">
		and id=#{id}
		</if>
		<if test="username!=null and username!=''">
		and username like '%${username}%'
		</if>
	</select>
```

注意：要做不等于空字符串校验。

#### 5.2 `<where>`

<where/>可以自动处理第一个and，相当于上面的语句。

```xml
<select id="findUserList" parameterType="user" resultType="user">
    select * from user 
    <where>
        <if test="id!=null and id!=''">
            and id=#{id}
        </if>
        <if test="username!=null and username!=''">
            and username like '%${username}%'
        </if>
    </where>
</select>
```

#### 5.3 `<foreach>`

```xml
<select id = "findUserByIds" parameter="user" resultType="user">
	select * from user where name = #{name}
    <if test="ids!=null and ids.size>0">
        <foreach collection="ids" open=" and id in(" close=")" item="id" separator="," >
            #{id}
        </foreach>
    </if>
</select>
```

标签属性说明：

| 属性       | 说明             |
| ---------- | ---------------- |
| collection | 传入的参数的键名 |
| open       | 以属性值开头     |
| close      | 以属性值结尾     |
| item       | 每个元素         |
| separator  | 隔断的字符       |

> collection属性说明：
>
> * 传递一个list实例或者数组作为参数的时候，Mybatis自动将他包装在一个Map中，用`list`或者`array`作为键，所以collection的值为`list`或`array`。
>
> * 传递一个map类型或者pojo类型的时候，会将map的键或pojo类的相应的属性值作为collection的值。

```
List<Integer> ids = new ArrayList<Integer>();
		ids.add(1);//查询id为1的用户
		ids.add(10); //查询id为10的用户
		// 这里传入的ids是一个list属性，但是在pojo类里面，使用ids作为collection的属性值
		queryVo.setIds(ids);
		List<User> list = userMapper.findUserList(queryVo);
```

#### 5.4 sql片段

可将重复的sql提取出来，使用时用include引用即可，最终达到sql重用的目的。

```xml
<!-- 传递pojo综合查询用户信息 -->
<select id="findUserList" parameterType="user" resultType="user">
    select * from user 
    <where>
        <if test="id!=null and id!=''">
            and id=#{id}
        </if>
        <if test="username!=null and username!=''">
            and username like '%${username}%'
        </if>
    </where>
</select>
```

等价于：

```xml
<sql id="query_user_where">
	<if test="id!=null and id!=''">
		and id=#{id}
	</if>
	<if test="username!=null and username!=''">
		and username like '%${username}%'
	</if>
</sql>

<select id="findUserList" parameterType="user" resultType="user">
    select * from user 
    <where>
        <include refid="query_user_where"/>
    </where>
</select>
```

注意：如果引用其它mapper.xml的sql片段，则在引用时需要加上namespace`<include refid="namespace.sql片段”/>`

### 6. 关联查询

案例：查询所有订单信息，关联查询下单用户信息。

注意：因为一个订单信息只会是一个人下的订单，所以从查询订单信息出发关联查询用户信息为一对一查询。如果从用户信息出发查询用户下的订单信息则为一对多查询，因为一个用户可以下多个订单。

#### 6.1 一对一查询

查询订单信息关联查询用户信息

（1）定义专门的pojo类接收查询的字段

```
public class OrdersCustom extends Orders {

	private String username;// 用户名称
	private String address;// 用户地址
get/set。。。。
}
```

* Mapper.xml文件的编写

```xml
<select id="findOrdersList" resultType="cn.itcast.mybatis.po.OrdersCustom">
    SELECT
    orders.*,
    user.username,
    user.address
    FROM
    orders,	user
    WHERE orders.user_id = user.id 
</select>
```

* 测试

```java
Public void testfindOrdersList()throws Exception{
    //获取session
    SqlSession session = sqlSessionFactory.openSession();
    //获限mapper接口实例
    UserMapper userMapper = session.getMapper(UserMapper.class);
    //查询订单信息
    List<OrdersCustom> list = userMapper.findOrdersList();
    System.out.println(list);
    //关闭session
    session.close();
}
```

（2）使用assocation完成关联查询，pojo属性中有另一类的属性

```java
public class Orders{
	private Integer id;
	private String userId;
	private String note;
	// 引入另一方的对象
	private User user;
}
```

* Mapper.xml的编写

```xml
<!-- 查询订单关联用户信息使用resultmap -->
	<resultMap type="cn.itheima.po.Orders" id="orderUserResultMap">
		<id column="id" property="id"/>
		<result column="user_id" property="userId"/>
		<result column="number" property="number"/>
		<result column="createtime" property="createtime"/>
		<result column="note" property="note"/>
		<!-- 一对一关联映射 -->
		<!-- 
		property:Orders对象的user属性
		javaType：user属性对应 的类型
		 -->
		<association property="user" javaType="cn.itcast.po.User">
			<!-- column:user表的主键对应的列  property：user对象中id属性-->
			<id column="user_id" property="id"/>
			<result column="username" property="username"/>
			<result column="address" property="address"/>
		</association>
	</resultMap>
	<!-- resultMap指定orderUserResultMap-->
	<select id="findOrdersWithUserResultMap" resultMap="orderUserResultMap">
		SELECT
			o.id,
			o.user_id,
			o.number,
			o.createtime,
			o.note,
			u.username,
			u.address
		FROM
			orders o
		JOIN `user` u ON u.id = o.user_id
	</select>
```

`<association>`：表示进行关联查询单条记录

* property：表示关联查询的结果存储在cn.itcast.mybatis.po.Orders的user属性中
* javaType：表示关联查询的结果类型
* <id property=*"id"* column=*"user_id"*/>：查询结果的user_id列对应关联对象的id属性，这里是`<id>`标签表示user_id是关联查询对象的唯一标识。
* <result property=*"username"* column=*"username"*/>：查询结果的username列对应关联对象的username属性。

#### 6.2 一对多查询

查询用户下的订单信息

```
public class User {
	private Integer id;
	private String userName;
	// 引入多方的集合
	private List<Orders> orders;
}
```

* Mapper.xml的配置

```xml
<resultMap type="cn.itheima.po.user" id="userOrderResultMap">
    <!-- 用户信息映射 -->
    <id property="id" column="id"/>
    <result property="username" column="username"/>
    <result property="birthday" column="birthday"/>
    <result property="sex" column="sex"/>
    <result property="address" column="address"/>
    <!-- 一对多关联映射 -->
    <collection property="orders" ofType="cn.itheima.po.Orders">
        <id property="id" column="oid"/>	
        <!--用户id已经在user对象中存在，此处可以不设置-->
        <!-- <result property="userId" column="id"/> -->
        <result property="number" column="number"/>
        <result property="createtime" column="createtime"/>
        <result property="note" column="note"/>
    </collection>
</resultMap>
<select id="getUserOrderList" resultMap="userOrderResultMap">
    SELECT
    u.*, o.id oid,
    o.number,
    o.createtime,
    o.note
    FROM
    `user` u
    LEFT JOIN orders o ON u.id = o.user_id
</select>
```

collection部分定义了用户关联的订单信息，表示关联查询结果集。

* property="orders"：关联查询的结果集存储在User对象的上哪个属性。
* ofType="orders"：指定关联查询的结果集中的对象类型即List中的对象类型。此处可以使用别名，也可以使用全限定名。
* <id />及<result/>的意义同一对一查询。

### 7. Mybatis整合spring

SqlSessionFactory对象应该放到spring容器中作为单例存在。数据库的连接以及数据库连接池事务管理都交给spring容器来完成。

* 传统dao的开发方式中，应该从spring容器中获得sqlsession对象。

* Mapper代理形式中，应该从spring容器中直接获得mapper的代理对象。

配置文件的配置：

* SqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<typeAliases>
		<package name="cn.itcast.mybatis.pojo"/>
	</typeAliases>
	<mappers>
		<mapper resource="sqlmap/User.xml"/>
	</mappers>
</configuration>
```

* applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">

	<!-- 加载配置文件 -->
	<context:property-placeholder location="classpath:db.properties" />
	<!-- 数据库连接池 -->
	<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
		destroy-method="close">
		<property name="driverClassName" value="${jdbc.driver}" />
		<property name="url" value="${jdbc.url}" />
		<property name="username" value="${jdbc.username}" />
		<property name="password" value="${jdbc.password}" />
		<property name="maxActive" value="10" />
		<property name="maxIdle" value="5" />
	</bean>
	<!-- mapper配置 -->
	<!-- 让spring管理sqlsessionfactory 使用mybatis和spring整合包中的 -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<!-- 数据库连接池 -->
		<property name="dataSource" ref="dataSource" />
		<!-- 加载mybatis的全局配置文件 -->
		<property name="configLocation" value="classpath:mybatis/SqlMapConfig.xml" />
	</bean>
</beans>
```

#### 7.1 传统的dao开发方式

传统的dao开发方式还是需要自己手动实例化SqlSessionFactory、SqlSession等会话类，Mybatis整合后就不用写那么多代码，使用Spring的依赖注入就可以。

* dao实现类

```java
// 需要继承SqlSessionDaoSupport类
public class UserDaoImpl extends SqlSessionDaoSupport implements UserDao {    
	@Override
	public User findUserById(int id) throws Exception {
		SqlSession session = getSqlSession();
		User user = session.selectOne("test.findUserById", id);
		//不能关闭SqlSession，让spring容器来完成
		//session.close();
		return user;
	}

	@Override
	public void insertUser(User user) throws Exception {
		SqlSession session = getSqlSession();
		session.insert("test.insertUser", user);
		session.commit();
		//session.close();
	}
}
```

* 配置dao

```xml
<!-- 配置UserDao实现类 -->
	<bean id="userDao" class="cn.itcast.dao.UserDaoImpl">
		<property name="sqlSessionFactory" ref="sqlSessionFactory"/>
	</bean>
```

* 测试方法

```java
private ApplicationContext applicationContext;
@Before
public void setUp() throws Exception{
	String configLocation = "classpath:spring/ApplicationContext.xml";
	//初始化spring运行环境
	applicationContext = new ClassPathXmlApplicationContext(configLocation);
}

测试:
@Test
public void testFindUserById() throws Exception {
	UserDao userDao = (UserDao) applicationContext.getBean("userDao");
	User user = userDao.findUserById(1);
	System.out.println(user);
}
```

#### 7.2 Mapper动态代理方式

* 配置mapper代理

```xml
<bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
    <property name="mapperInterface" value="cn.itcast.mybatis.mapper.UserMapper"/>
    <property name="sqlSessionFactory" ref="sqlSessionFactory"></property>
</bean>
```

* 测试方法

```java
public class UserMapperTest {

	private ApplicationContext applicationContext;
	@Before
	public void setUp() throws Exception {
		applicationContext = new ClassPathXmlApplicationContext("classpath:spring/applicationContext.xml");
	}

	@Test
	public void testGetUserById() {
		UserMapper userMapper = applicationContext.getBean(UserMapper.class);
		User user = userMapper.getUserById(1);
		System.out.println(user);
	}
}
```

#### 7.3 扫描包形式配置Mapper

```xml
<!-- 使用扫描包的形式来创建mapper代理对象 -->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	<property name="basePackage" value="cn.itcast.mybatis.mapper"></property>
<bean/>
```

[spring和mybatis的整合开发(传统Dao开发方式)](https://www.cnblogs.com/jasonboren/p/10594586.html)

### 8. Mybatis逆向工程

mybatis官方提供逆向工程,可以使用它通过数据库中的表来自动生成Mapper接口和映射文件(单表增删改查)和Po类。可以极大的简化开发。

#### 8.1 逆向工程的配置

* 逆向工程的生成mapper和dto类的配置文件generatorConfig.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
	<context id="testTables" targetRuntime="MyBatis3">
		<commentGenerator>
			<!-- 是否去除自动生成的注释 true：是 ： false:否 -->
			<property name="suppressAllComments" value="true" />
		</commentGenerator>
		<!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
		<jdbcConnection driverClass="com.mysql.jdbc.Driver"
			connectionURL="jdbc:mysql://localhost:3306/mybatis" userId="root"
			password="admin">
		</jdbcConnection>
        <!--oracle的驱动-->
		<!-- <jdbcConnection driverClass="oracle.jdbc.OracleDriver"
			connectionURL="jdbc:oracle:thin:@127.0.0.1:1521:yycg" 
			userId="yycg"
			password="yycg">
		</jdbcConnection> -->

		<!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和 
			NUMERIC 类型解析为java.math.BigDecimal -->
		<javaTypeResolver>
			<property name="forceBigDecimals" value="false" />
		</javaTypeResolver>

		<!-- targetProject:生成PO类的位置 -->
		<javaModelGenerator targetPackage="cn.itcast.ssm.po"
			targetProject=".\src">
			<!-- enableSubPackages:是否让schema作为包的后缀 -->
			<property name="enableSubPackages" value="false" />
			<!-- 从数据库返回的值被清理前后的空格 -->
			<property name="trimStrings" value="true" />
		</javaModelGenerator>
        <!-- targetProject:mapper映射文件生成的位置 -->
		<sqlMapGenerator targetPackage="cn.itcast.ssm.mapper" 
			targetProject=".\src">
			<!-- enableSubPackages:是否让schema作为包的后缀 -->
			<property name="enableSubPackages" value="false" />
		</sqlMapGenerator>
		<!-- targetPackage：mapper接口生成的位置 -->
		<javaClientGenerator type="XMLMAPPER"
			targetPackage="cn.itcast.ssm.mapper" 
			targetProject=".\src">
			<!-- enableSubPackages:是否让schema作为包的后缀 -->
			<property name="enableSubPackages" value="false" />
		</javaClientGenerator>
		<!-- 指定数据库表 -->
		<table tableName="items"></table>
		<table tableName="orders"></table>
		<table tableName="orderdetail"></table>
		<table tableName="user"></table>
		<!-- <table schema="" tableName="sys_user"></table>
		<table schema="" tableName="sys_role"></table>
		<table schema="" tableName="sys_permission"></table>
		<table schema="" tableName="sys_user_role"></table>
		<table schema="" tableName="sys_role_permission"></table> -->
		
		<!-- 有些表的字段需要指定java类型
		 <table schema="" tableName="">
			<columnOverride column="" javaType="" />
		</table> -->
	</context>
</generatorConfiguration>
```

* 使用java类生成mapper文件  

```java
Public void generator() throws Exception{
		List<String> warnings = new ArrayList<String>();
		boolean overwrite = true;
		File configFile = new File("generatorConfig.xml"); 
		ConfigurationParser cp = new ConfigurationParser(warnings);
		Configuration config = cp.parseConfiguration(configFile);
		DefaultShellCallback callback = new DefaultShellCallback(overwrite);
		MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config,
				callback, warnings);
		myBatisGenerator.generate(null);
	}
	Public static void main(String[] args) throws Exception {
		try {
			GeneratorSqlmap generatorSqlmap = new GeneratorSqlmap();
			generatorSqlmap.generator();
		} catch (Exception e) {
			e.printStackTrace();
		}
}
```

#### 8.2 逆向工程的注意事项

（1）重复逆向工程产生的问题

Mapper文件内容不覆盖而是追加，XXXMapper.xml文件已经存在时，如果进行重新生成则mapper.xml文件内容不被覆盖而是进行内容追加，结果导致mybatis解析失败。Mybatis自动生成的po及mapper.java文件不是内容而是直接覆盖没有此问题。

解决方法：删除原来已经生成的mapper xml文件再进行生成。

（2）Table schema问题

Schma即数据库模式，oracle中一个用户对应一个schema，可以理解为用户就是schema。当Oralce数据库存在多个schema可以访问相同的表名时，使用mybatis生成该表的mapper.xml将会出现mapper.xml内容重复的问题，结果导致mybatis解析错误。

解决方法：在table中填写schema，如下：

```
<table schema="XXXX" tableName=" " >
```

XXXX即为一个schema的名称，生成后将mapper.xml中schema前缀批量去掉，如果不去掉当oracle用户变更了sql语句将查询失败。可以将mapper.xml文件中批量替换：“from XXXX.”为空。

### 9.Mybatis和Hibernate的区别

Mybatis：Mybatis学习门槛低，不需要掌握很复杂的文件配置，例如：`<one-to-many>`，级联，懒加载，fetch等标签及属性，并且开发快，通过；是一个半自动orm框架，这主要体现在开发者还是需要手写sql语句，来控制查询到或者关联到的对象，并且在级联操作这方面没有hibernate擅长；不过Mybatis可以进行sql语句的调优。

Hibernate：Hibernate学习成本过高，因为他需要学习QBC控制配置文件，来更加灵活高效地查询和持久化数据；Hibernate对象关系映射能力强，它在pojo类中有set属性和对象属性来表示一对多、多对多的关系，在配置文件中有many-to-one、many-to-many等关系；是一个完全的orm框架，可以用java面向对象的思想进行数据的curd；易于数据库迁移，因为有数据库方言的配置，并且curd代码都是用对象进行操作，没有sql语句，这样避免不同数据库的sql语句不同。

> 个人理解：
>
> 从开发角度来说，Mybatis的学习门槛低，可以通过学习及其简单的配置，例如：只需要会sql语句，学习动态sql标签，其他的都由Mapper动态代理，甚至可以通过逆向工程来完成，配置文件基本上都可以在网上查找到，并且可以通过sql调优提高性能；Hibernate学习成本高，需要学习one-to-many、many-to-many标签，cascade、lazy、fetch等属性，QBC、HQL、SQL等查询方式来更好的进行查询操作及持久化类维护，不过也正是因为这样，Hibernate具有更强的映射关系，一对多、一对一、多对多的关系都可以表示出来，并且有级联、懒加载、迫切连接等操作，可以提高性能及减少代码的同时，也更符合面向对象的思想，不过这也给开发者提高了操作难度。
>
> 从其他角度来说Hibernate具有数据库方言的配置，更容易进行数据库迁移；Mybatis是一个半自动的ORM框架，需要开发者进行sql语句的编写。
>
> 总体来说Mybatis就是配置简单，开发快；Hibernate更加面向对象化，具有更强的映射关系，在查询及数据库迁移方面更加简单。

# SpringMVC学习

## 一、基本概念

### 1. SpringMVC是什么

SpringMVC属于表现层框架，它是Spring框架的一部分，用于接收请求，返回参数。

## 二、SpringMVC的基本配置

引入jar包

### 1. 配置文件

* 创建springMVC.xml文件

```xml
<?xmlversion="1.0"encoding="UTF-8"?>
<beansxmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
        http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd">

	<context:component-scanbase-package="cn.itcast.springmvc.controller"/>
	
</beans>
```

* 配置web.xml

```xml
<servlet>
	<servlet-name>springmvc</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<init-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:springmvc.xml</param-value>
	</init-param>
</servlet>
<servlet-mapping>
	<servlet-name>springmvc</servlet-name>
	<url-pattern>*.action</url-pattern>
</servlet-mapping>
```

> 对于web.xml中`/`和`/*`的区别理解：
>
> `<url-pattern>/</url-pattern>`不会匹配`*.jsp`这样的带后缀的请求，所以不会进入到DispatcherServlet类去与Controller中的方法进行匹配，相当于拦截掉了；所以对于没有后缀的请求，进入到DispatcherServlet时，DispatcherServlet会将url与Controller中的方法进行映射，然后进一步处理。由于Servlet容器中内置了JspServlet，所以虽然不会被DispatcherServlet匹配，但是会被JspServlet匹配，可以进行进一步处理。
>
> `<url-pattern>/*<url-pattern>`会匹配所有的url请求，当获取RestFul风格的无后缀请求时，DispatcherServlet会成功匹配并映射给Controller方法，但是当返回`*.jsp`这样的后缀页面时，也会被DispatcherServlet匹配去映射给Controller方法，当没有这个url对应的方法时抛出404异常。
>
> 所以推荐使用`<url-pattern>/</url-pattern>`，对于有后缀的url，解决方法如下：
>
> ```xml
> <!-- 使用默认的servlet对url请求进行处理 -->
> <servlet-mapping>
> 	<servlet-name>defalut</servlet-name>
> 	<url-pattern>*.html</url-pattern>
> </servlet-mapping>
> <!-- /* 匹配的优先权最高，所以放在最后面防止覆盖 -->
> <servlet-mapping>
> 	<servlet-name>springmvc</servlet-name>
> 	<url-pattern>*.action</url-pattern>
> </servlet-mapping>
> ```

[深入理解web.xml中配置/和/*的区别](https://blog.csdn.net/z69183787/article/details/83580925 )

### 2. 测试程序

* Controller文件

```java
@Controller
publicclass ItemController {

	@RequestMapping("/itemList")
	public ModelAndView itemList() throws Exception {
		
		List<Items>itemList = new ArrayList<>();
		
		//商品列表
		Items items_1 = new Items();
		items_1.setName("联想笔记本_3");
		items_1.setPrice(6000f);
		items_1.setDetail("ThinkPad T430 联想笔记本电脑！");
		
		Items items_2 = new Items();
		items_2.setName("苹果手机");
		items_2.setPrice(5000f);
		items_2.setDetail("iphone6苹果手机！");
		
		itemList.add(items_1);
		itemList.add(items_2);
		//创建modelandView对象
		ModelAndView modelAndView = new ModelAndView();
		//添加model
		modelAndView.addObject("itemList", itemList);
		//添加视图
		modelAndView.setViewName("/WEB-INF/jsp/itemList.jsp");
//		modelAndView.setViewName("itemsList");	
		returnmodelAndView;
	}

}
```

## 三、SpringMVC的深入了解

### 1. SpringMVC架构

#### 1.1 SpringMVC的执行流程

![SpringMVC的执行流程](F:\学习笔记\SSM\SpringMVC的执行流程.png)

* 用户发送HttpRequest请求到前端控制器DispatcherServlet，前端控制器调用处理映射器HandlerMapping。
* 处理映射器根据URL找到具体的处理器Handler，生成处理器对象以及拦截器链一并返回给前端控制器。
* 前端控制器通过调用处理器适配器HandlerAdapter来执行处理器，并将处理器返回的ModelAndView。
* 前端控制器将ModelAndView传给视图解析器ViewResolver，解析后返回具体的视图对象View给前端控制器。
* 前端控制器对视图进行渲染，将数据模型填充到视图中。
* 将页面响应给客户端。

#### 1.2 SpringMVC的组件说明

* 前端控制器DispatcherServlet
  * 前端控制器是SpringMVC整个流程的控制中心，相当于MVC架构中的C，负责接收返回数据。用户请求到达前端控制器，由它调用其它组件处理用户的请求，降低组件之间的耦合性。
* 处理器映射器HandlerMapping
  * 处理器映射器根据url请求找到相对应的处理器Handler。
* 处理器Handler
  * 是一个后端控制器，在前端控制器的控制下处理用户的请求，进行具体的业务操作。
* 处理器适配器HandlerAdapter
  * 是一种适配器模式的适用，通过扩展适配器可以适配更多的处理器。
* 视图解析器ViewResolver
  * 负责将处理结果生成视图View，ViewResolver首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。 
* 视图View
  * springmvc框架提供了很多的View视图类型的支持，包括：jstlView、freemarkerView、pdfView等。我们最常用的视图就是jsp。
* 模型和视图ModelAndView
  * Model中存放返回页面的数据，View存返回页面的位置

### 2. 配置文件的解析

#### 2.1 不使用注解的方式

（1）配置文件

```xml
 <!-- 配置HandlerMapping -->
 <bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
     <property name="mappings">
         <props>
         	<prop key="/hello.do">helloController</prop>
         </props>
     </property>
 </bean>	

<!-- 注册controller的bean -->
 <bean id="helloController" class="test.TestController"/>

<!-- 配置HandlerAdapter -->
 <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"/>

<!-- 配置ViewResolver -->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/"/> 
    <property name="suffix" value=".jsp"/> 
</bean> 
```

（2）配置文件详解

* RequestMappingHandlerMapping
  * 注解式处理器映射器，对类中标记@ResquestMapping的方法进行映射，根据ResquestMapping定义的url匹配ResquestMapping标记的方法，匹配成功返回HandlerMethod对象给前端控制器，HandlerMethod对象中封装url对应的方法Method。 
  * 从spring3.1版本开始，废除了DefaultAnnotationHandlerMapping的使用，推荐使用RequestMappingHandlerMapping完成注解式处理器映射。
  * 配置如下：

```xml
<!-- 对于没有标记@RequestMapping注解的配置HandlerMapping -->
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="mappings">
        <props>
            <prop key="/hello.do">helloController</prop>
        </props>
    </property>
</bean>	

<!-- 对类中标记了@RequestMapping注解进行映射器映射 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>

// @RequestMapping：定义请求url到处理器功能方法的映射
```

* RequestMappingHandlerAdapter
  * 注解式处理器适配器，对标记@ResquestMapping的方法进行适配。
  * 从spring3.1版本开始，废除了AnnotationMethodHandlerAdapter的使用，推荐使用RequestMappingHandlerAdapter完成注解式处理器适配。
  * 配置如下：

```xml
<!--注解适配器 --> 
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"/>
```

* InternalResourceViewResolver  
  * 视图解析器，支持JSP视图解析
  * viewClass：JstlView表示JSP模板页面需要使用JSTL标签库，所以classpath中必须包含jstl的相关jar 包。此属性可以不设置，默认为JstlView。
  * prefix 和suffix：查找视图页面的前缀和后缀，最终视图的址为：前缀+**逻辑视图名**+后缀，逻辑视图名需要在controller中返回ModelAndView指定，比如逻辑视图名为hello，则最终返回的jsp视图地址 “WEB-INF/jsp/hello.jsp”。
  * 配置如下：

```xml
<beanclass="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <propertyname="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
    <propertyname="prefix" value="/WEB-INF/jsp/"/>
    <propertyname="suffix" value=".jsp"/>
</bean>
```

#### 2.2 使用注解的方式

（1）配置文件

```xml
<!-- 配置注解扫描 -->
<context:component-scan base-package="test"></context:component-scan>

<!-- 配置mvc注解扫描 -->
<mvc:annotation-driven></mvc:annotation-driven>

<!-- 配置ViewResolver -->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/"/> 
    <property name="suffix" value=".jsp"/> 
</bean> 
```

（2）配置文件详解

* `<mvc:annotation-driven></mvc:annotation-driven>`，
  * springmvc使用`<mvc:annotation-driven>`自动加载RequestMappingHandlerMapping和RequestMappingHandlerAdapter，可用在springmvc.xml配置文件中使用`<mvc:annotation-driven>`替代注解处理器和适配器的配置。

* `<context:component-scan base-package="springmvc.controller"></context:component-scan>`
  * 使用组件扫描器，省去在文件中配置每个controller类的繁琐。使用`<context:component-scan>`自动扫描标记@controller的控制器类，把这些类注册为bean。

### 3.  Handler方法默认支持的参数类型

处理器形参中添加如下类型的参数，处理适配器会默认识别并进行赋值。

#### 3.1 HttpServletRequest

通过request对象获取请求信息

#### 3.2 HttpServletResponse

通过response处理响应信息

#### 3.3 HttpSession

通过session对象得到session中存放的对象

#### 3.4 Model/ModelMap

* ModelMap是Model接口的实现类，通过Model或ModelMap向页面传递数据，如下：

```java
//调用service查询商品信息
Items item = itemService.findItemById(id);
model.addAttribute("item", item);

//  页面通过${item.XXXX}获取item对象的属性值
<p>${item.name}</p>
```

* 使用Model和ModelMap的效果一样，如果直接使用Model，springmvc会实例化ModelMap。
  *  如果使用Model则可以不使用ModelAndView对象，Model对象可以向页面传递数据，View对象则可以使用String返回值替代。

```java
@RequestMapping("/itemEdit")
publicString itemEdit(HttpServletRequest request, Model model) {
    //从Request中取id
    String strId = request.getParameter("id");
    Integer id = null;
    //如果id有值则转换成int类型
    if (strId != null && !"".equals(strId)) {
    	id = newInteger(strId);
    } else {
    //出错
    	return null;
    }
    Items items = itemService.getItemById(id);
    model.addAttribute("item", items);
    return"editItem";
    
    /*
    创建ModelAndView
    //ModelAndView modelAndView = new ModelAndView();
    //向jsp传递数据
    modelAndView.addObject("item", items);
    //设置跳转的jsp页面
    modelAndView.setViewName("editItem");
    return modelAndView;
    */
}
```

### 4. 参数绑定

#### 4.1 简单数据的参数绑定

当请求的参数名称和处理器形参名称一致时会将请求参数与形参进行绑定，从Request取参数的方法可以进一步简化。

```java
@RequestMapping("/itemEdit")
public String itemEdit(Integer id, Model model) {
    Items items = itemService.getItemById(id);
    //向jsp传递数据
    model.addAttribute("item", items);
    //设置跳转的jsp页面
    return"editItem";
}
```

（2）支持的数据类型

参数类型推荐使用包装数据类型，因为基础数据类型不可以为null。

* 整形：Integer、int

* 字符串：String

* 单精度：Float、float

* 双精度：Double、double

* 布尔型：Boolean、boolean

说明：对于布尔类型的参数，请求的参数值为true或false。

```
// 处理器方法
public String editItem(Model model,Integer id,Boolean status) throws Exception{

}

// 请求url
http://localhost:8080/xxx.action?id=2&status=false
```

#### 4.2 @RequestParam参数绑定

使用@RequestParam常用于处理简单类型的绑定。定义如下：

```
// 将传入的参数item_id绑定在形参id上
public String editItem(@RequestParam(value="item_id",required=true) String id) {    

}
```

* value：参数名字，即入参的请求参数名字，如value=“item_id”表示请求的参数区中的名字为item_id的参数的值将传入。
* required：是否必须，默认是true，表示请求中一定要有相应的参数，否则将报：TTP Status 400 - Required Integer parameter 'XXXX' is not present。
* defaultValue：默认值，表示如果请求中没有同名参数时的默认值

#### 4.3 绑定pojo类型

如果提交的参数很多，或者提交的表单中的内容很多的时候可以使用pojo接收数据。要求pojo对象中的属性名和表单中input的name属性一致。

```
// 1.输入框
<input type="text" name="name"/>
<input type="text" name="price"/>
<input type="text" name="items.name" />
<input type="text" name="items.price" />

// 2.pojo定义
public class order{
	private String name;
	private Float price;
	private String dec;
	// 包装的pojo类
	private Items items;
	
	....
}

// 3.controller类
@RequestMapping("/updateitem")
public String updateItem(Items items) {
    itemService.updateItem(items);
    return "success";
}
```

### 5. 绑定参数类型时的问题

#### 5.1 参数乱码

（1）post请求乱码

```xml
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

（2）get请求乱码

* 修改tomcat配置文件添加编码与工程编码一致，如下：

```xml
<Connector URIEncoding="utf-8" connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443"/>
```

* 另外一种方法对参数进行重新编码：

```java
String userName new 
// ISO8859-1是tomcat默认编码，需要将tomcat编码后的内容按utf-8编码
String(request.getParamter("userName").getBytes("ISO8859-1"),"utf-8")
```

####  5.2 日期类型参数的报错

由于日期数据有很多种格式，所以springmvc没办法把字符串转换成日期类型。需要自定义参数绑定。前端控制器接收到请求后，找到注解形式的处理器适配器，对RequestMapping标记的方法进行适配，并对方法中的形参进行参数绑定。在springmvc这可以在处理器适配器上自定义Converter进行参数绑定。如果使用`<mvc:annotation-driven/>`可以在此标签上进行扩展。

（1）第一种配置方式

* 自定义Converter

```java
public class DateConverter implements Converter<String Date> {

	@Override
	public Date convert(String source) {
		SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		try {
			return simpleDateFormat.parse(source);
		} catch (ParseException e) {
			e.printStackTrace();
		}
		return null;
	}
}
```

* 配置Converter

```xml
<!-- 加载注解驱动 -->
<mvc:annotation-drivenconversion-service="conversionService"/>
<!-- 转换器配置 -->
<bean id="conversionService"
class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
    <propertyname="converters">
        <set>
        	<beanclass="cn.itcast.springmvc.convert.DateConverter"/>
        </set>
    </property>
</bean>
```

（2）第二种配置方式

```xml
<!-- 扫描带Controller注解的类 -->
<context:component-scanbase-package="cn.itcast.springmvc.controller"/>

<!-- 转换器配置 -->
<bean id="conversionService" class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
    <property name="converters">
        <set>
            <bean class="cn.itcast.springmvc.convert.DateConverter"/>
        </set>
    </property>
</bean>

<!-- 自定义webBinder -->
<bean id="customBinder"	class="org.springframework.web.bind.support.ConfigurableWebBindingInitializer">
    <property name="conversionService"ref="conversionService"/>
</bean>

<!--注解适配器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
    <property name="webBindingInitializer"ref="customBinder"></property>
</bean>

<!-- 注解处理器映射器 -->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>

<!-- 加载注解驱动 -->
<!-- <mvc:annotation-driven/> -->

<!-- 视图解析器 -->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
        <!-- jsp前缀 -->
        <property name="prefix"value="/WEB-INF/jsp/"/>
        <!-- jsp后缀 -->
        <property name="suffix"value=".jsp"/>
    </bean>
</beans>
```

注意：此方法需要独立配置处理器映射器、适配器，不再使用`<mvc:annotation-driven/>`

### 6. 高级参数绑定

#### 6.1 数组绑定

* 页面的书写

```html
<c:forEach items="${itemList }" var="item">
    <tr>
        <td><input name="ids" value="${item.id}" type="checkbox"></td>
        <td>${item.name }</td>
        <td>${item.price }</td>
        <td>
            <fmt:formatDate value="${item.createtime}" pattern="yyyy-MM-dd HH:mm:ss"/>			</td>
        <td>${item.detail }</td>
        <td>
            <a href="${pageContext.request.contextPath }/itemEdit.action?id=${item.id}">
                修改
            </a>	
        </td>
    </tr>
</c:forEach>
```

（1）直接简单数据绑定

```java
@RequestMapping("/queryitem")
public String queryItem(QueryVo queryVo, String[] ids) {
    System.out.println(queryVo.getItems().getName());
    System.out.println(queryVo.getItems().getPrice());
    System.out.println(ids.toString());
    return null;
}
```

（2）pojo类中定义数组属性

```java
public class QueryVo{
	private String[] ids;
	......
}

@RequestMapping("/queryitem")
public String queryItem(QueryVo queryVo) {
    System.out.println(queryVo.ids.toString());
    return null;
}
```

#### 6.2 表单数据绑定到list

List中存放对象，并将定义的List放在包装类中，使用包装pojo对象接收。

* pojo类准备

```java
public class QueryVo{
	private List<Items> itemsList;
	......
}
```

* 页面的书写

```html
<c:forEach items="${itemList }" var="item" varStatus="status">
    <tr>
        <!-- name属性名称要等于vo中的接收的属性名 -->
        <td>
            <input type="checkbox" name="ids" value="${item.id }"/>
            <input type="hidden" name="itemsList[${status.index }].id" value="${item.id }"/>
        </td>
        <td><input type="text" name="itemsList[${status.index }].name" value="${item.name }"/></td>
        <td><input type="text" name="itemsList[${status.index }].price" value="${item.price }"/></td>
        <td><input type="text" name="itemsList[${status.index }].createtime" 
                   value="<fmt:formatDate value="${item.createtime}" pattern="yyyy-MM-dd HH:mm:ss"/>"/></td>
        <td><input type="text" name="itemsList[${status.index }].detail" value="${item.detail }"/></td>

        <td><a href="${pageContext.request.contextPath }/items/itemEdit/${item.id}">修改</a></td>

    </tr>
</c:forEach>
```

注意：接收List类型的数据必须是pojo的属性，方法的形参为List类型无法正确接收到数据。

#### 6.3 `<c:foreach>`标签

```html
// 起始索引值为1，终止索引值为10，每隔3个索引搜索一次
<c:forEach items="${itemList }" var="item" varStatus="status" begin="1" end="10" step="2">
......
</c:forEach>
```

varStatus属性常用参数总结下：

| 属性参数          | 说明                                                  |
| ----------------- | ----------------------------------------------------- |
| ${status.index}   | 输出行号，从0开始                                     |
| ${status.count}   | 输出行号，从1开始                                     |
| ${status.current} | 当前这次迭代的（集合中的）项                          |
| ${status.first}   | 判断当前项是否为集合中的第一项，返回值为true或false   |
| ${status.last}    | 判断当前项是否为集合中的最后一项，返回值为true或false |

### 7. controller方法返回值

#### 7.1  返回ModelAndView

controller方法中定义ModelAndView对象并返回，对象中可添加model数据、指定view。

```java
//创建ModelAndView
ModelAndView modelAndView = new ModelAndView();
//向jsp传递数据
modelAndView.addObject("item", items);
//设置跳转的jsp页面
modelAndView.setViewName("editItem");
return modelAndView;
```

#### 7.2 返回void

在controller方法形参上可以定义request和response，使用request或response指定响应结果。

（1）request转向页面

```java
request.getRequestDispatcher("页面路径").forward(request, response);
```

（2）response页面重定向

```
response.sendRedirect("url")
```

 （3）response指定响应结果，例如响应json数据如下：

```
response.setCharacterEncoding("utf-8");
response.setContentType("application/json;charset=utf-8");
response.getWriter().write("json串");
```

#### 7.3 返回字符串

（1）逻辑视图名

 controller方法返回字符串可以指定逻辑视图名，通过视图解析器解析为物理视图地址。

```
//指定逻辑视图名，经过视图解析器解析为jsp物理路径：/WEB-INF/jsp/item/editItem.jsp
return "item/editItem";
```

 （2）Redirect重定向

Contrller方法返回结果重定向到一个url地址。

```
//重定向到queryItem.action地址,request无法带过去
return "redirect:queryItem.action";
```

redirect方式相当于“response.sendRedirect()”，转发后浏览器的地址栏变为转发后的地址，因为转发即执行了一个新的request和response。由于新发起一个request原来的参数在转发时就不能传递到下一个url，如果要传参数可以/item/queryItem.action后边加参数，如：/item/queryItem?...&…..。

（3）forward转发

controller方法执行后继续执行另一个controller方法。

```
//结果转发到editItem.action，request可以带过去
return "forward:editItem.action";
```

 forward方式相当于“request.getRequestDispatcher().forward(request,response)”，转发后浏览器地址栏还是原来的地址。转发并没有执行新的request和response，而是和转发前的请求共用一个request和response。所以转发前请求的参数在转发后仍然可以读取到。

### 8. @RequestMapping注解

 通过RequestMapping注解可以定义不同的处理器映射规则。

#### 8.1 URL路径映射

@RequestMapping(value="/item")或@RequestMapping("/item）

value的值是数组，可以将多个url映射到同一个方法

#### 8.2 窄化请求映射

在class上添加@RequestMapping(url)指定通用请求前缀， 限制此类下的所有方法请求url必须以请求前缀开头，通过此方法对url进行分类管理。

```
// 访问地址为：/item/queryItem

// @RequestMapping放在类名上边，设置请求前缀 
@Controller
@RequestMapping("/item")
public class Test{

	// @RequestMapping放在方法名上边
	@RequestMapping("/queryItem ")
	public String test(){
	
	}
}
```

#### 8.3 请求方法限定 

（1）限定GET方法，@RequestMapping(method = RequestMethod.GET)

```
// 例如
@RequestMapping(value="/editItem",method=RequestMethod.GET)

// 如果通过Post访问则报错
HTTP Status 405 - Request method 'POST' not supported
```

（2）限定POST方法，@RequestMapping(method = RequestMethod.*POST*)

```
// 例如
@RequestMapping(value="/editItem",method=RequestMethod.POST)

// 如果通过Post访问则报错
HTTP Status 405 - Request method 'GET' not supported
```

 （3）GET和POST都可以，@RequestMapping(method={RequestMethod.GET,RequestMethod.POST})

### 9. 异常处理器

 springmvc在处理请求过程中出现异常信息交由异常处理器进行处理，自定义异常处理器可以实现一个系统的异常处理逻辑。

#### 9.1 异常处理思路

系统中异常包括两类：预期异常和运行时异常RuntimeException，前者通过捕获异常从而获取异常信息，后者主要通过规范代码开发、测试通过手段减少运行时异常的发生。系统的dao、service、controller出现都通过throws Exception向上抛出，最后由springmvc前端控制器交由异常处理器进行异常处理。

#### 9.2 自定义异常类

为了区别不同的异常，通常根据异常类型自定义异常类，这里我们创建一个自定义系统异常，如果controller、service、dao抛出此类异常说明是系统预期处理的异常信息。

```java
public class CustomException extends Exception {

	/** serialVersionUID*/
	private static final long serialVersionUID = -5212079010855161498L;
	public CustomException(String message){
		super(message);
		this.message = message;
	}
	//异常信息
	private String message;
	public String getMessage() {
		return message;
	}
	public void setMessage(String message) {
		this.message = message;
	}
}
```

#### 9.3 自定义异常处理器  

```java
public class CustomExceptionResolver implements HandlerExceptionResolver {

	@Override
	public ModelAndView resolveException(HttpServletRequest request,
			HttpServletResponse response, Object handler, Exception ex) {

		ex.printStackTrace();

		CustomException customException = null;
		
		//如果抛出的是系统自定义异常则直接转换
		if(ex instanceof CustomException){
			customException = (CustomException)ex;
		}else{
			//如果抛出的不是系统自定义异常则重新构造一个系统错误异常。
			customException = new CustomException("系统错误，请与系统管理 员联系！");
		}
		
		ModelAndView modelAndView = new ModelAndView();
		modelAndView.addObject("message", customException.getMessage());
		modelAndView.setViewName("error");

		return modelAndView;
	}

}

```

#### 9.4 错误页面

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt"  prefix="fmt"%> 
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>错误页面</title>

</head>
<body>
您的操作出现错误如下：<br/>
${message }
</body>
</html>
```

#### 9.5 异常处理器配置

在SpringMVC.xml中配置

```xml
<!-- 异常处理器 -->
<bean id="handlerExceptionResolver" class="cn.itcast.ssm.controller.exceptionResolver.CustomExceptionResolver"/>
```

#### 9.6 异常测试

修改商品信息，id输入错误提示商品信息不存在。

修改controller方法“editItem”，调用service查询商品信息，如果商品信息为空则抛出异常：

```java
// 调用service查询商品信息
		Items item = itemService.findItemById(id);
		
		if(item == null){
			throw new CustomException("商品信息不存在!");
		}
```

### 10. 上传图片

#### 10.1 配置虚拟目录 

在tomcat上配置图片虚拟目录，在tomcat下conf/server.xml中添加：

```
<Context docBase="F:\develop\upload\temp" path="/pic" reloadable="false"/>
```

访问http://localhost:8080/pic 即可访问F:\develop\upload\temp下的图片。

####  10.2 配置解析器

导入jar包

```xml
<!-- 文件上传 -->
	<bean id="multipartResolver"
		class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<!-- 设置上传文件的最大尺寸为5MB -->
		<property name="maxUploadSize">
			<value>5242880</value>
		</property>
	</bean>
```

#### 10.3 controller图片上传

```java
@RequestMapping("/editItemSubmit")
public String editItemSubmit(Items items, MultipartFile pictureFile)throws Exception{

    //原始文件名称
    String pictureFile_name =  pictureFile.getOriginalFilename();
    //新文件名称
    String newFileName = UUID.randomUUID().toString()+pictureFile_name.substring(pictureFile_name.lastIndexOf("."));

    //上传图片
    File uploadPic = new java.io.File("F:/develop/upload/temp/"+newFileName);

    if(!uploadPic.exists()){
    	uploadPic.mkdirs();
    }
    //向磁盘写文件
    pictureFile.transferTo(uploadPic);

.....
}
```

#### 10.4 图片上传页面

```html
<form id="itemForm" action="${pageContext.request.contextPath}/item/editItemSubmit.action"
		method="post" enctype="multipart/form-data">
		<input type="hidden" name="pic" value="${item.pic }" />

<tr>
	<td>商品图片</td>
	<td><c:if test="${item.pic !=null}">
			<img src="/pic/${item.pic}" width=100 height=100 />
			<br />
		</c:if> <input type="file" name="pictureFile" /></td>
</tr>
```

注意：文件上传的form添加`enctype="multipart/form-data"`。

### 11.  json数据交互

#### 11.1 @RequestBody

@RequestBody注解用于读取http请求的内容(字符串)，通过springmvc提供的HttpMessageConverter接口将读到的内容转换为json、xml等格式的数据并绑定到controller方法的参数上。实现接收http请求的json数据，将json字符串数据转换为java对象

注意：使用@RequestBody注解时，是用于接收Content-Type为application/json类型的请求，数据类型是JSON字符串："{"aaa":"111","bbb":"222"}"；不使用@RequestBody注解时，可以接收Content-Type为application/x-www-form-urlencoded类型的请求所提交的数据，数据格式：aaa=111&bbb=222。form表单提交以及jQuery的post()方法所发送的请求就是这种类型。

#### 11.2 @ResponseBody

该注解用于将Controller的方法返回的对象，通过HttpMessageConverter接口转换为指定格式的数据如：json,xml等，通过Response响应给客户端。实现将controller方法返回对象转换为json响应给客户端。

#### 11.3 请求json，响应json实现：

（1）环境准备

Springmvc默认用MappingJacksonHttpMessageConverter对json数据进行转换，需要加入jackson的包。

（2）配合json转换器

```xml
<!--注解适配器 -->
	<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
		<property name="messageConverters">
		<list>
		<bean class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter"></bean>
		</list>
		</property>
	</bean>
```

注意：如果使用则不用定义上边的内容。

（3）controller的编写

```java
// 商品修改提交json信息，响应json信息
	@RequestMapping("/editItemSubmit_RequestJson")
	public @ResponseBody Items editItemSubmit_RequestJson(@RequestBody Items items) throws Exception {
		System.out.println(items);
		//itemService.saveItem(items);
		return items;
	}
```

（4）前端编写

```js
	function request_json(){
		$.ajax({
			type:"post",
			url:"${pageContext.request.contextPath }/item/editItemSubmit_RequestJson.action",
			contentType:"application/json;charset=utf-8",
			data:'{"name":"测试商品","price":99.9}',
			success:function(data){
				alert(data);
			}
		});
	}
```

### 12. RESTful支持

#### 12.1 什么是restful？

Restful就是一个资源定位及资源操作的风格。不是标准也不是协议，只是一种风格，是对http协议的诠释。

资源定位：互联网所有的事物都是资源，要求url中没有动词，只有名词。没有参数

Url格式：http://blog.csdn.net/beat_the_world/article/details/45621673

资源操作：使用put、delete、post、get，使用不同方法对资源进行操作。分别对应添加、删除、修改、查询。一般使用时还是post和get。Put和Delete几乎不使用。

#### 12.2 添加DispatcherServlet的rest配置

 通过restful方式进行查询

```xml
<servlet>
    <servlet-name>springmvc-servlet-rest</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring/springmvc.xml</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>springmvc-servlet-rest</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

#### 12.3 URL 模板模式映射

@RequestMapping(value="/ viewItems/{id}")：{×××}占位符，请求的URL是`/viewItems/1`或`/viewItems/2`，通过在方法中使用@PathVariable获取{×××}中的×××变量。

@PathVariable用于将请求URL中的模板变量映射到功能处理方法的参数上，如果RequestMapping中表示为"/viewItems/{id}"，id和形参名称一致，则@PathVariable不用指定名称。

```java
@RequestMapping("/viewItems/{id}") 
public @ResponseBody viewItems(@PathVariable("id") String id,Model model) throws Exception{
    //方法中使用@PathVariable获取useried的值，使用model传回页面
    //调用 service查询商品信息
    ItemsCustom itemsCustom = itemsService.findItemsById(id);
    return itemsCustom;
}
```

商品查询的controller方法也改为rest实现：

```javascript
// 查询商品列表
  @RequestMapping("/queryItem")
  public ModelAndView queryItem() throws Exception {
    // 商品列表
    List<Items> itemsList = itemService.findItemsList(null);
    // 创建modelAndView准备填充数据、设置视图
    ModelAndView modelAndView = new ModelAndView();
    // 填充数据
    modelAndView.addObject("itemsList", itemsList);
    // 视图
    modelAndView.setViewName("item/itemsList");
    return modelAndView;
}
```

#### 12.4 静态资源访问

如果在DispatcherServlet中设置url-pattern为`/`则必须对静态资源进行访问处理，spring mvc 的`<mvc:resources mapping="" location="">`实现对静态资源进行映射访问。

如下是对js文件访问配置：

```xml
<!-- 1.对每种静态资源进行映射访问 -->
<mvc:resources location="/js/" mapping="/js/**"/>

<!-- 2.使用默认的servlet进行映射访问处理，推荐 -->
<mvc:default-servlet-handler/>
```

### 13. 拦截器

SpringMVC的处理器拦截器类似于Servlet 开发中的过滤器Filter，用于对处理器进行预处理和后处理。

#### 13.1 拦截器定义

实现HandlerInterceptor接口，如下：

```java
Public class HandlerInterceptor1 implements HandlerInterceptor{

	/**
	 * controller执行前调用此方法
	 * 返回true表示继续执行，返回false中止执行
	 * 这里可以加入登录校验、权限拦截等
	 */
	@Override
	Public boolean preHandle(HttpServletRequest request,
			HttpServletResponse response, Object handler) throws Exception {
		// TODO Auto-generated method stub
		Return false;
	}
	/**
	 * controller执行后但未返回视图前调用此方法
	 * 这里可在返回用户前对模型数据进行加工处理，比如这里加入公用信息以便页面显示
	 */
	@Override
	Public void postHandle(HttpServletRequest request,
			HttpServletResponse response, Object handler,
			ModelAndView modelAndView) throws Exception {
		// TODO Auto-generated method stub
		
	}
	/**
	 * controller执行后且视图返回后调用此方法
	 * 这里可得到执行controller时的异常信息
	 * 这里可记录操作日志，资源清理等
	 */
	@Override
	Public void afterCompletion(HttpServletRequest request,
			HttpServletResponse response, Object handler, Exception ex)
			throws Exception {
		// TODO Auto-generated method stub
	}
}
```

#### 13.2 拦截器配置

（1）针对某种mapping配置拦截器

```xml
<bean
	class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping">
	<property name="interceptors">
		<list>
			<ref bean="handlerInterceptor1"/>
			<ref bean="handlerInterceptor2"/>
		</list>
	</property>
</bean>

<bean id="handlerInterceptor1" class="springmvc.intercapter.HandlerInterceptor1"/>
<bean id="handlerInterceptor2" class="springmvc.intercapter.HandlerInterceptor2"/>
```

（2）针对所有mapping配置拦截器

```xml
<!--拦截器 -->
<mvc:interceptors>
	<!--多个拦截器,顺序执行 -->
	<mvc:interceptor>
		<mvc:mapping path="/**"/>
		<bean class="cn.itcast.springmvc.filter.HandlerInterceptor1"></bean>
	</mvc:interceptor>
	<mvc:interceptor>
		<mvc:mapping path="/**"/>
		<bean class="cn.itcast.springmvc.filter.HandlerInterceptor2"></bean>
	</mvc:interceptor>
    <!-- 拦截特定请求 -->
    <mvc:interceptor>
		<mvc:mapping path="/test"/>
		<bean class="cn.itcast.springmvc.filter.HandlerInterceptor2"></bean>
	</mvc:interceptor>
</mvc:interceptors>
```

#### 13.3 执行流程

（1）正常执行，按照xml配置的顺序进行执行：

```
HandlerInterceptor1..preHandle..

HandlerInterceptor2..preHandle..
 

HandlerInterceptor2..postHandle..

HandlerInterceptor1..postHandle..


HandlerInterceptor2..afterCompletion..

HandlerInterceptor1..afterCompletion..
```

（2）中断流程测试

* HandlerInterceptor1的preHandler方法返回false，HandlerInterceptor2返回true，运行流程如下：

```
HandlerInterceptor1..preHandle..
```

 看出第一个拦截器的preHandler方法返回false后第一个拦截器只执行了preHandler方法，其它两个方法没有执行，第二个拦截器的所有方法不执行，且controller也不执行了。

* HandlerInterceptor1的preHandler方法返回true，HandlerInterceptor2返回false，运行流程如下：

```
HandlerInterceptor1..preHandle..

HandlerInterceptor2..preHandle..

HandlerInterceptor1..afterCompletion..
```

 看出第二个拦截器的preHandler方法返回false后第一个拦截器的postHandler没有执行，第二个拦截器的postHandler和afterCompletion没有执行，且controller也不执行了。

* 总结：
  * preHandle按拦截器定义顺序调用；postHandler按拦截器定义逆序调用；afterCompletion按拦截器定义逆序调用。
  * postHandler在拦截器链内所有拦截器返成功调用，afterCompletion只有preHandle返回true才调用。

### 14. SpringMVC和Struts2的区别

1、 SpringMVC的入口是一个servlet即前端控制器，而Struts2入口是一个filter过虑器。

2、 SpringMVC是基于方法开发(一个url对应一个方法)，请求参数传递到方法的形参，可以设计为单例或多例(建议单例)，Struts2是基于类开发，传递参数是通过类的属性，只能设计为多例。

3、 Struts采用值栈存储请求和响应的数据，通过OGNL存取数据， SpringMVC通过参数解析器是将request请求内容解析，并给方法形参赋值，将数据和视图封装成ModelAndView对象，最后又将ModelAndView中的模型数据通过reques域传输到页面。

> 为什么Struts2是多例而SpringMVC是多例呢？
>
> Struts2中参数绑定是通过属性驱动或模型驱动的方式，多例的情况下，每个请求都会创建一个新的action，这样绑定后的属性数据不会共享，防止数据同时被多个地方修改；单例模式下，多个线程调用会导致数据被修改掉，而且Struts2是通过ValueStack的方式将数据返回给前端，单例的情况下会导致线程不安全。
>
> SpringMVC是参数绑定是通过方法传入的形参的方式，在单例的情况下，多线程会共享成员变量，但是不会共享方法，这样可以保证线程安全，而且不用new很多对象，提高性能。

> 个人理解：
>
> 从开发角度来说，SpringMVC可以通过注解的方式映射url和方法，不用进行很多的配置，这样可以简化开发过程，并且SpringMVC通过自动参数绑定和ModelAndView的方式进行参数的获取和返回，这样可以极大的提高开发效率。
>
> 从性能上来说SpringMVC单例模式的性能高于Struts2的性能。