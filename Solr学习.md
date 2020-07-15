#  Solr学习

## 一、基本概念

### **1.Solr是什么**

Solr 是Apache下的一个顶级开源项目，采用Java开发，它是**基于Lucene的全文搜索服务器**。Solr提供了比Lucene更为丰富的查询语言，同时实现了可配置、可扩展，并对索引、搜索性能进行了优化。 
Solr可以独立运行，**运行在Jetty、Tomcat等这些Servlet容器中**。Solr 索引的实现方法很简单，用 POST 方法向 Solr 服务器发送一个描述 Field 及其内容的 XML 文档，Solr根据xml文档添加、删除、更新索引 。Solr 搜索只需要发送 HTTP GET 请求，然后对 Solr 返回Xml、json等格式的查询结果进行解析，组织页面布局。Solr不提供构建UI的功能，但是Solr提供了一个管理界面，通过管理界面可以查询Solr的配置和运行情况。

### **2.Solr与Lucene的区别**

* Lucene是一个开放源代码的**全文检索引擎工具包**，它不是一个完整的全文检索引擎，Lucene提供了完整的查询引擎和索引引擎，目的是为软件开发人员提供一个简单易用的工具包，以方便的在目标系统中实现全文检索的功能，或者以Lucene为基础构建全文检索引擎。
*  Solr的目标是打造一款企业级的搜索引擎系统，它是一个**搜索引擎服务**，可以独立运行，通过Solr可以非常快速的**构建企业的搜索引擎**，通过Solr也可以高效的完成**站内搜索功能**。

![Solr和Lucene的区别](.\Solr\Solr和Lucene的区别.png)

### **3.为什么学习Solr**

在一些大型门户网站、电子商务网站等都需要站内搜索功能，使用传统的数据库查询方式实现搜索无法满足一些高级的搜索需求，比如：搜索速度要快、搜索结果按相关度排序、搜索内容格式不固定等，这里就需要使用全文检索技术实现搜索功能。一般企业中将数据全部放入数据库中，由于查询的时候需要使用like模糊查询，模糊查询数据库中使用的是全表扫描算法，这样效率低级，所以需要使用全文检索,来优化查询速度。

* **使用Lucene实现：**单独使用Lucene实现站内搜索需要开发的工作量较大，主要表现在：索引维护、索引性能优化、搜索性能优化等，因此不建议采用。

* **使用Solr实现：**基于Solr实现站内搜索扩展性较好并且可以减少程序员的工作量，因为Solr提供了较为完备的搜索引擎解决方案，因此在门户、论坛等系统中常用此方案。

## 二、Solr安装及配置

### **1.Solr的下载**

从Solr[官网](http://lucene.apache.org/solr/)下载，根据运行环境，Solr分为Linux和Windows版。

[Solr使用指南](https://wiki.apache.org/solr/FrontPage)

### **2.Solr的文件夹结构**

![Solr的文件夹结构](./Solr/Solr的文件夹结构.png) 

> bin --solr的运行脚本
>
> contrib --solr的一些软件/插件，用于增强solr的功能。
>
> dist --该目录包含build过程中产生的war和jar文件，以及相关的依赖文件。
>
> docs --solr的API文档
>
> licenses --solr相关的一些许可信息
>
> example  --solr工程的例子目录：
>
> > example/solr --该目录是一个包含了默认配置信息的Solr的Core目录
> >
> > example/multicore --该目录包含了在Solr的multicore中设置的多个Core目录。
> >
> > example/webapps --该目录中包括一个solr.war，该war可作为solr的运行实例工程

### **3.运行环境**

solr 需要运行在一个Servlet容器中，Solr4.10.3要求jdk使用1.7以上，Solr默认提供Jetty（java写的Servlet容器），本教程使用Tocmat作为Servlet容器，环境如下：
`Solr：Solr4.10.3；Jdk：jdk1.7.0_72；Tomcat：apache-tomcat-7.0.53`。

### **4.Solr整合tomcat**

**4.1 Solr Home与Solr Core**

创建一个Solr Home目录，Solr Home是Solr运行的主目录，目录中包括了运行Solr实例所有的配置文件和数据文件，Solr实例就是Solr Core，**一个Solr Home可以包括多个Solr Core**（Solr实例），每个Solr Core提供单独的搜索和索引服务。

example/solr是一个Solr Home目录结构，如下：

![Solr Home和Solr Core目录](.\Solr\Solr Home和Solr Core目录.png) 

Solr Core名称不固定，一个Solr运行实例对外单独提供索引和搜索接口。Solr Home中可以创建多个Solr运行实例（Solr Core）。一个Solr Core对应一个索引目录，conf是SolrCore的配置文件目录，data目录存放索引文件需要创建

**4.2 整合步骤**

4.2.1 把solr的dist目录下的war包复制到tomcat的webapp目录下，改名为solr.war。

4.2.2 solr.war解压，使用压缩工具解压或者启动tomcat自动解压，解压之后删除solr.war。

4.2.3 把\solr-4.10.3\example\lib\ext目录下的所有的jar包添加到tomcat下solr\WEB-INF\lib工程中

4.2.4 配置Solr Home和Solr Core

* 创建一个名称为mySolrHome的文件夹（存放solr所有配置文件），复制example目录下的文件到mySolrHome。

* 在mySolrHome下有一个文件夹叫做collection1这就是一个Solr Core(solr的实例)。一个Solr Core相当于mysql中一个数据库，Solr Core之间是相互隔离。

* 在Solr Core中有一个文件夹叫做conf，包含了索引Solr Core(的配置信息。在conf文件夹下有一个solrconfig.xml，配置实例的相关信息。**如果使用默认配置可以不用做任何修改。**

**solrconfig.xml的配置信息：**

| 标签           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| Lib            | solr服务依赖的扩展包，默认的路径是collection1\lib文件夹，如果没有就创建一个 |
| dataDir        | 配置了索引库的存放路径，默认路径是collection1\data文件夹，如果没有data文件夹，会自动创建。 |
| requestHandler | 不同搜索操作时配置的url                                      |

![查询时的url](.\Solr\查询时的url.png) 

![更新时的url](.\Solr\更新时的url.png) 

4.2.5 告诉solr服务器配置文件也就是mySolrHome的位置，修改tomcat/webapp/solr/WEB-INF/web.xml，使用jndi（类似于创建依赖配置文件）的方式告诉solr服务器。**Solr/home名称必须是固定的。**

![web.xml的配置](.\Solr\web.xml的配置.png)  

4.2.6 启动tomcat，访问http://localhost:8080/solr/。

## 三、Solr后台管理

### **1.管理界面**

![Solr的管理界面](.\Solr\Solr的管理界面.png)) 

### **2.Dashboard**

仪表盘，显示了该Solr实例开始启动运行的时间、版本、系统资源、jvm等信息。

### **3.Logging**

Solr运行日志信息

### **4.Cloud**

即Solr Cloud，即Solr云（集群），当使用Solr Cloud模式运行时会显示此菜单，如下图是Solr Cloud的管理界面：

![Solr的集群界面](.\Solr\Solr的集群界面.png) 

### **5.Core Admin**

Solr Core的管理界面。Solr Core 是Solr的一个独立运行实例单位，它可以对外提供索引和搜索服务，一个Solr工程可以运行多个Solr Core（Solr实例），一个Solr Core对应一个索引目录。

```
添加solrcore

// 1. 复制collection1改名为collection2

// 2. 修改collection2文件下core.properties name=collection2

// 3. 重启tomcat
```

### **6.java properties**

Solr在JVM 运行环境中的属性信息，包括类路径、文件编码、jvm内存设置等信息。

### **7.Tread Dump**

显示Solr Server中当前活跃线程信息，同时也可以跟踪线程运行栈信息。

### **8.Core selector**

选择一个Solr Core进行详细操作，如下：

![Core selector的界面](.\Solr\Core selector的界面.png) 

### **9.Analysis**

通过此界面可以测试索引分析器和搜索分析器的执行情况。

![Analysis的界面](.\Solr\Analysis的界面.png) 

### **10.Dataimport**

可以定义数据导入处理器，从关系数据库将数据导入到Solr索引库中。

### **11.Document**

通过此菜单Solr可以（默认根据id）创建索引、更新索引、删除索引等操作，界面如下：

![Document的界面](.\Solr\Document的界面.png) 

* **注意：**文档和索引的增加和修改必须要有id主键域，没有会报错

### **12.Query**

通过/select执行搜索索引，必须指定“q”查询条件方可搜索。

![Query的界面](.\Solr\Query的界面.png) 

## 四、中文分析器

### **1.配置中文分析器**

**1.1 配置Schema.xml**

schema.xml在Solr Core的conf目录下，它是Solr数据表配置文件，它定义了加入索引的数据的数据类型的。主要包括FieldTypes、Fields和其他的一些缺省设置。

**1.2 FieldType域类型定义**

在FieldType定义的时候最重要的就是定义这个类型的数据在**建立索引和进行查询**的时候要使用的**分析器analyzer的类型，其下包括分词器和过滤器**。

下边“text_general”是Solr默认提供的FieldType，通过它说明FieldType定义的内容：

![默认FieldType节点配置](.\Solr\默认FieldType节点配置.png) 

**FieldType标签属性：**

| 标签属性             | 说明                                                         |
| -------------------- | ------------------------------------------------------------ |
| name                 | FieldType的名称                                              |
| class                | 是Solr提供的包solr.TextField，solr.TextField 允许用户通过分析器来定制索引和查询，分析器包括一个分词器（tokenizer）和多个过滤器（filter） |
| positionIncrementGap | 可选属性，定义在同一个文档中此类型数据的空白间隔，避免短语匹配错误，此值相当于Lucene的短语查询设置slop值（slop是指两个term的位置之间允许的最大间隔距离），根据经验设置为100（设置为100相当于两个term之间最多可以有100单词）。 |

**1.3 Field定义**

Solr索引字段包含在<fields></fields>中。在fields结点内定义具体的field，filed定义包括name，type（为之前定义过的各种FieldType名称，也可以是Solr封装后的String、Long等基本类型）,indexed（是否被索引）,stored（是否被储存），multiValued（是否存储多个值）等属性。

* 注意：是否存储和是否索引无关, 索引后就能查询,不索引就不能根据这个域搜索, 存储后就能取出来里面的内容,不存储就取不出这个域内容。

```xml
<fields>
	<field name="name" type="text_general" indexed="true" stored="true"/>
	<field name="features" type="text_general" indexed="true" stored="true" multiValued="true"/>
</fields>
```

* **注意：**域名和类型必须先定义后使用,如果没有定义就使用会报错

**multiValued：**该Field如果要存储多个值时设置为true，Solr允许一个Field存储多个值，比如存储一个用户的好友id（多个），商品的图片（多个，大图和小图），通过使用Solr查询要看出**返回给客户端是数组**。

![img](F:\学习笔记\Solr\Field标签的multiValued属性.png) 

**1.4 uniqueKey**

Solr中默认定义（`<uniqueKey>id</uniqueKey>`）唯一主键key为id域，Solr在删除、更新索引时使用id域进行判断，也可以自定义唯一主键。

* **注意：**在创建索引时必须指定唯一约束。

**1.5 copyField复制域**

copyField复制域，可以将多个Field复制到一个Field中，以便进行统一的检索。

```xml
// 比如：输入关键字搜索title标题content内容，定义title、content、text的域：
<fields>
	<field name="title" type="text_general" indexed="true" stored="true"/>
	<field name="content" type="text_general" indexed="true" stored="true" multiValued="true"/>
    <field name="text" type="text_general" indexed="true" stored="true"/>
</fields>

//根据关键字只搜索text域的内容就相当于搜索title和content，将title和content复制到text中
<copyFoeld source="title" dest="text"/>
<copyFoeld source="content" dest="text"/>
```

**1.6 dynamicField（动态字段）**

动态字段就是不用指定具体的名称，只要定义字段名称的规则，任何符合这个规则的字段都被认为是符合这个定义的。

```
// 定义一个 dynamicField，任何以_i结尾的字段符合这个定义，例如：product_title_t
<dynamicField name="*_t" type="text_general" indexed="true" stored="true"/>
```

创建索引：自定义Field名product_title_t，它和scheam.xml中的dynamicField规则匹配成功

![dynamicField案例之创建索引](F:\学习笔记\Solr\dynamicField案例之创建索引.png) 

搜索索引：

![dynamicField案例之创建索引](F:\学习笔记\Solr\dynamicField案例之搜索索引.png) 

### **2.安装中文分词器**

使用IKAnalyzer中文分析器。

* 把IKAnalyzer2012FF_u1.jar添加到solr/WEB-INF/lib目录下。

* 复制IKAnalyzer的配置文件和自定义词典和停用词词典到solr的新建的一个classes文件夹下。

* 在schema.xml中添加一个自定义的fieldType，使用中文分析器。

```xml
<fieldType name="text_ik" class="solr.TextField">
    <analyzer class="org.wltea.analyzer.lucene.IKAnalyzer"/>
</fieldType>
```

* 定义field，指定field的type属性为text_ik

```xml
<field name="title_ik" type="text_ik" indexed="true" stored="true" />
<field name="content_ik" type="text_ik" indexed="true" stored="false" multiValued="true"/>
```

* 重启tomcat

* 测试

![安装中文分词器的测试](F:\学习笔记\Solr\安装中文分词器的测试.png) 

### **3.设置业务系统Field**

如果不使用Solr提供的Field，可以针对具体的业务需要自定义一套Field。

```xml
// 例如：定义商品信息Field
<field name="product_name" type="text_ik" indexed="true" stored="true"/>
<field name="product_price"  type="float" indexed="true" stored="true"/>
<field name="product_description" type="text_ik" indexed="true" stored="false" />
<field name="product_picture" type="string" indexed="false" stored="true" />
<field name="product_catalog_name" type="string" indexed="true" stored="true" />
<field name="product_keywords" type="text_ik" indexed="true" stored="false" multiValued="true"/>

<copyField source="product_name" dest="product_keywords"/>
<copyField source="product_description" dest="product_keywords"/>
```

## 五、维护索引

### **1.添加/更新文档**

![维护索引之添加单个文档](F:\学习笔记\Solr\维护索引之添加单个文档.png) 

### **2.批量导入数据**

使用dataimport插件批量导入数据。

* 把dataimport插件依赖的jar包和mysql的数据库驱动添加到Solr Core\lib（collection1\lib）中

![维护索引之批量导入数据](F:\学习笔记\Solr\维护索引之批量导入数据.png) 

* 配置solrconfig.xml文件，添加一个requestHandler。

```xml
<requestHandler name="/dataimport" 
                class="org.apache.solr.handler.dataimport.DataImportHandler">
    <lst name="defaults">
        <str name="config">data-config.xml</str>
    </lst>
</requestHandler> 
```

* 创建一个data-config.xml，保存到collection1\conf\目录下

```xml
<?xml version="1.0" encoding="UTF-8" ?>  
<dataConfig>   
    <dataSource type="JdbcDataSource"   
                driver="com.mysql.jdbc.Driver"   
                url="jdbc:mysql://localhost:3306/lucene"   
                user="root"   
                password="root"/>   
    <document>   
        <entity name="product" query="SELECT pid,name,catalog_name,price,description,picture FROM products ">
            <field column="pid" name="id"/> 
            <field column="name" name="product_name"/> 
            <field column="catalog_name" name="product_catalog_name"/> 
            <field column="price" name="product_price"/> 
            <field column="description" name="product_description"/> 
            <field column="picture" name="product_picture"/> 
        </entity>   
    </document>   
</dataConfig>
```

第四步：重启tomcat后点击“execute”按钮导入数据

![维护索引之批量导入数据2](F:\学习笔记\Solr\维护索引之批量导入数据2.png) 

* **注意：**导入数据前会先清空索引库，然后再导入。

### **3.删除文档**

在documents的Document(s)中书写，删除索引格式如下：

**3.1 删除制定ID的索引** 

```xml
<delete>
	<id>8</id>
</delete>

<commit/>
//需要进行保存
```

**3.2 删除查询到的索引数据** 

```xml
<delete>
	<query>product_catalog_name:幽默杂货</query>
</delete>
```

**3.3 删除所有索引数据**

```xml
 <delete>
	<query>*:*</query>
</delete>
```

### **4.查询索引**

通过/select搜索索引，Solr制定一些参数完成不同需求的搜索：

**4.1 q - 查询字符串（必须）**

如果查询所有使用`*:*`。

![维护索引之查询索引](F:\学习笔记\Solr\维护索引之查询索引.png) 

**4.2 fq - （filter query，就是过滤查询）**

在q查询符合结果中同时是fq查询符合的

例如过滤查询价格从1到20的记录：

![维护索引之查询索引2](F:\学习笔记\Solr\维护索引之查询索引2.png)

也可以在“q”查询条件中使用product_price:[1 TO 20]：

![维护索引之查询索引3](F:\学习笔记\Solr\维护索引之查询索引3.png) 

也可以使用“*”表示无限：

```
20以上：product_price:[20 TO *]
20以下：product_price:[* TO 20]
```

**4.3 sort（排序）**

格式：sort=<field name>+<desc|asc>[,<field name>+<desc|asc>]… 

例如按价格降序：

![维护索引之查询索引4](F:\学习笔记\Solr\维护索引之查询索引4.png)

**4.4 start和rows**

start（分页起始）和rows（指定返回结果最多有多少条记录）配合使用可以达到分页效果。

例如显示前10条：

![维护索引之查询索引5](F:\学习笔记\Solr\维护索引之查询索引5.png) 

**4.5 fl**

指定返回那些字段内容，用逗号或空格分隔多个。

例如显示商品图片、商品名称、商品价格：

![img](F:\学习笔记\Solr\维护索引之查询索引6.png) 

**4.6 df**

指定一个搜索Field

![img](F:\学习笔记\Solr\维护索引之查询索引7.png) 

也可以在SolrCore目录 中conf/solrconfig.xml文件中指定默认搜索Field，指定后就可以直接在“q”查询条件中输入关键字。

![维护索引之查询索引8](F:\学习笔记\Solr\维护索引之查询索引8.png) 

**4.7 wt（writer type）**

指定输出格式，可以有 xml, json, php, phps, 后面 solr-1.3增加的，默认关闭。

**4.8 hl** 

是否高亮 ,设置高亮Field，设置格式前缀和后缀。

![img](F:\学习笔记\Solr\维护索引之查询索引9.png) 

## 六、使用SolrJ管理索引库

### **1.SolrJ是什么**

solrj是访问Solr服务的java客户端，提供索引和搜索的请求方法，SolrJ通常在嵌入在业务系统中，通过SolrJ的API接口操作Solr服务。

![Solrj搜索库](F:\学习笔记\Solr\Solrj搜索库.png)

### **2.依赖的jar包**

![Solrj搜索库依赖的jar包](F:\学习笔记\Solr\Solrj搜索库依赖的jar包.png) 

### **3.添加文档**

**3.1 实现步骤**

* 创建一个java工程

* 导入jar包：包括solrJ的jar包，还有以下的jar包

![Solrj搜索库依赖的jar包2](F:\学习笔记\Solr\Solrj搜索库依赖的jar包2.png) 

* 和Solr服务器建立连接。HttpSolrServer对象建立连接。

* 创建一个SolrInputDocument对象，然后添加域。

* 将SolrInputDocument添加到索引库。

* 提交。

**3.2 代码实现**

```java
//向索引库中添加索引
@Test
public void addDocument() throws Exception {
    //和solr服务器创建连接
    //参数：solr服务器的地址
    SolrServer solrServer = new HttpSolrServer("http://localhost:8080/solr");
    //创建一个文档对象
    SolrInputDocument document = new SolrInputDocument();
    //向文档中添加域
    //第一个参数：域的名称，域的名称必须是在schema.xml中定义的
    //第二个参数：域的值
    document.addField("id", "c0001");
    document.addField("title_ik", "使用solrJ添加的文档");
    document.addField("content_ik", "文档的内容");
    document.addField("product_name", "商品名称");
    //把document对象添加到索引库中
    solrServer.add(document);
    //提交修改
    solrServer.commit();

}
```

### **4.删除文档**

**4.1 根据id删除**

```java
//删除文档，根据id删除
@Test
public void deleteDocumentByid() throws Exception {
    //创建连接
    SolrServer solrServer = new HttpSolrServer("http://localhost:8080/solr");
    //根据id删除文档
    solrServer.deleteById("c0001");
    //提交修改
    solrServer.commit();
}
```

**4.2 根据查询删除**

查询语法完全支持Lucene的查询语法。

```java
//根据查询条件删除文档
@Test
public void deleteDocumentByQuery() throws Exception {
    //创建连接
    SolrServer solrServer = new HttpSolrServer("http://localhost:8080/solr");
    //根据查询条件删除文档
    solrServer.deleteByQuery("*:*");
    //提交修改
    solrServer.commit();
}
```

### **5.修改文档**

在solrJ中修改没有对应的update方法，只有add方法，只需要添加一条新的文档，和被修改的文档id一致就，可以修改了，本质上就是先删除后添加。

### **6.查询文档**

**6.1 简单查询**

```java
//查询索引
@Test
public void queryIndex() throws Exception {
    //创建连接
    SolrServer solrServer = new HttpSolrServer("http://localhost:8080/solr");
    //创建一个query对象
    SolrQuery query = new SolrQuery();
    //设置查询条件
    query.setQuery("*:*");
    //执行查询
    QueryResponse queryResponse = solrServer.query(query);
    //取查询结果
    SolrDocumentList solrDocumentList = queryResponse.getResults();
    //共查询到商品数量
    System.out.println("共查询到商品数量:" + solrDocumentList.getNumFound());
    //遍历查询的结果
    for (SolrDocument solrDocument : solrDocumentList) {
        System.out.println(solrDocument.get("id"));
        System.out.println(solrDocument.get("product_name"));
        System.out.println(solrDocument.get("product_price"));
        System.out.println(solrDocument.get("product_catalog_name"));
        System.out.println(solrDocument.get("product_picture"));

    }
}
```

**6.2 复杂查询**

其中包含查询、过滤、分页、排序、高亮显示等处理。

```java
//复杂查询索引
@Test
public void queryIndex2() throws Exception {
    //创建连接
    SolrServer solrServer = new HttpSolrServer("http://localhost:8080/solr");
    //创建一个query对象
    SolrQuery query = new SolrQuery();
    //设置查询条件
    query.setQuery("钻石");
    //过滤条件
    query.setFilterQueries("product_catalog_name:幽默杂货");
    //排序条件
    query.setSort("product_price", ORDER.asc);
    //分页处理
    query.setStart(0);
    query.setRows(10);
    //结果中域的列表
    query.setFields("id","product_name","product_price","product_catalog_name","product_picture");
    //设置默认搜索域
    query.set("df", "product_keywords");
    //高亮显示
    query.setHighlight(true);
    //高亮显示的域
    query.addHighlightField("product_name");
    //高亮显示的前缀，可以对高亮区域进行类似html标签的编写
    query.setHighlightSimplePre("<em>");
    //高亮显示的后缀
    query.setHighlightSimplePost("</em>");
    //执行查询
    QueryResponse queryResponse = solrServer.query(query);
    //取查询结果
    SolrDocumentList solrDocumentList = queryResponse.getResults();
    //共查询到商品数量
    System.out.println("共查询到商品数量:" + solrDocumentList.getNumFound());
    //遍历查询的结果
    for (SolrDocument solrDocument : solrDocumentList) {
        System.out.println(solrDocument.get("id"));
        //取高亮显示
        String productName = "";
        Map<String, Map<String, List<String>>> highlighting = queryResponse.getHighlighting();
        List<String> list = highlighting.get(solrDocument.get("id")).get("product_name");
        //判断是否有高亮内容
        if (null != list) {
            productName = list.get(0);
        } else {
            productName = (String) solrDocument.get("product_name");
        }

        System.out.println(productName);
        System.out.println(solrDocument.get("product_price"));
        System.out.println(solrDocument.get("product_catalog_name"));
        System.out.println(solrDocument.get("product_picture"));

    }
}
```

## 七、SolrCloud使用教程和原理介绍

SolrCloud 是基于 Solr 和 Zookeeper 的分布式搜索方案，是正在开发中的 Solr4.0 的核心组件之一，它的主要思想是使用 Zookeeper 作为集群的配置信息中心。它有几个特色功能：

* 集中式的配置信息：使用ZK进行集中配置。启动时可以指定把Solr的相关配置文件上传Zookeeper，多机器共用。这些ZK中的配置不会再拿到本地缓存，Solr直接读取ZK中的配置信息。配置文件的变动，所有机器都可以感知到。另外，Solr的一些任务也是通过ZK作为媒介发布的。目的是为了容错。接收到任务，但在执行任务时崩溃的机器，在重启后，或者集群选出候选者时，可以再次执行这个未完成的任务。
* 自动容错：SolrCloud对索引分片，并对每个分片创建多个Replication。每个Replication都可以对外提供服务。一个Replication挂掉不会影响索引服务。更强大的是，它还能自动的在其它机器上帮你把失败机器上的索引Replication重建并投入使用。
* 近实时搜索：立即推送式的replication（也支持慢推送），可以在秒内检索到新加入索引。
* 查询时自动负载均衡：SolrCloud索引的多个Replication可以分布在多台机器上，均衡查询压力。如果查询压力大，可以通过扩展机器，增加Replication来减缓。

### **1.SolrCloud 的基本概念**

SolrCloud 是指 Solr 中一套新的潜在的分发能力。这种能力能够通过参数让你建立起一个高可用、容错的 Solr 服务集群。当你需要大规模，容错，分布式索引和检索能力时使用 SolrCloud(solr 云)。 

* **Collection：**在SolrCloud集群中逻辑意义上的完整的索引。它常常被划分为一个或多个Shard，它们使用相同的Config Set。如果Shard数超过一个，它就是分布式索引，SolrCloud让你通过Collection名称引用它，而不需要关心分布式检索时需要使用的和Shard相关参数。

* **Config Set：**Solr Core提供服务必须的一组配置文件。每个config set有一个名字。最小需要包括solrconfig.xml (SolrConfigXml)和schema.xml (SchemaXml)，除此之外，依据这两个文件的配置内容，可能还需要包含其它文件。它存储在Zookeeper中。Config sets可以重新上传或者使用upconfig命令更新，使用Solr的启动参数bootstrap_confdir指定可以初始化或更新它。

* **Core：**也就是Solr Core，一个Solr中包含一个或者多个Solr Core，每个Solr Core可以独立提供索引和查询功能，每个Solr Core对应一个索引或者Collection的Shard，Solr Core的提出是为了增加管理灵活性和共用资源。在SolrCloud中有个不同点是它使用的配置是在Zookeeper中的，传统的Solr core的配置文件是在磁盘上的配置目录中。

* **Leader：**赢得选举的Shard replicas。每个Shard有多个Replicas，这几个Replicas需要选举来确定一个Leader。选举可以发生在任何时间，但是通常他们仅在某个Solr实例发生故障时才会触发。当索引documents时，SolrCloud会传递它们到此Shard对应的leader，leader再分发它们到全部Shard的replicas。

* **Replica：**Shard的一个拷贝。每个Replica存在于Solr的一个Core中。一个命名为“test”的collection以numShards=1创建，并且指定replicationFactor设置为2，这会产生2个replicas，也就是对应会有2个Core，每个在不同的机器或者Solr实例。一个会被命名为test_shard1_replica1，另一个命名为test_shard1_replica2。它们中的一个会被选举为Leader。

* **Shard：**Collection的逻辑分片。每个Shard被化成一个或者多个replicas，通过选举确定哪个是Leader。

* **Zookeeper：**Zookeeper提供分布式锁功能，对SolrCloud是必须的。它处理Leader选举。Solr可以以内嵌的Zookeeper运行，但是建议用独立的，并且最好有3个以上的主机。

### **2.关于SolrCores和Collections**

对于单独运行的 Solr 实例，它有个东西叫 SolrCore(Solr.xml 中配置的),它是本质上独立的索引块。如果你打算多个索引块，你就创建多个 SolrCores。当同时部署 SolrCloud 的时候，独立的索引块可以跨越多个 Solr 实例。这意味着一个单独的索引块能由不同服务器设备上多个 SolrCore 的索引块组成。我们把组成一个逻辑索引块的所有 SolrCores 叫做一个独立索引块儿(collection)。一个独立索引块是本质上一个独立的跨越多个 SolrCore 索引块的索引块，同时索引块尽可能随着多余的设备进行缩放。如果你想把你的两个 SolrCore Solr 建立成 SolrCloud,你将有 2 个独立索引块，每个有多个独立里的 SolrCores 组成。 

[SolrCloud原理介绍](https://eksliang.iteye.com/blog/2110676)

### **3.启动** 

**3.1 下载 Solr4-Beta 或更高版本**

如果你还没了解，通过简单的 Solr 指南让自己熟悉 Solr。**注意**：在通过指南了解云特点前， 重设所有的配置和移除指南的文档，复制带有预先存在的 Solr 索引的例子目录，将导致文档计数关闭。Solr 内嵌使用了 Zookeeper 作为集群配置和协调运作的仓储，协调考虑作为一个包含所有 Solr 服务信息的分布式文件系统。 如果你想用一个其他的而不是 8983 作为 Solr 端口，去看下面’ Parameter Reference’部分下 的关于 solr.xml 注解 

**3.2 简单的2个 shard 集群** 

这个例子简单的创建了一个代表一个独立索引块的，两个不同的 shards 的，两个 solr 服务组成的集群。我们将需要两个 solr 服务器，简单的复制例子目录副本作为第二个服务器时， 要确保没有任何索引存在。 

```
rm -r example/solr/collection1/data/* 

cp -r example example2 
```

下面的命令启动了一个 solr 服务同时引导了一个新的 solr 集群 

```
cd example 

java -Dbootstrap_confdir=./solr/collection1/conf 

-Dcollection.configName=myconf -DzkRun -DnumShards=2 -jar start.jar 
```

参数定义

* -DzkRun 该参数将促使一个内嵌的 zookeeper 服务作为 Solr 服务的部分运行起来。 

* -Dbootstrap_confdir=./solr/collection1/conf 当我们还没有 zookeeper 配置时，这个参数导致本地路径./solr/conf 作为“myconf”配置被加载，“myconf”来自下面的“collection.configName”  。

* -Dcollection.configName=myconf 设置用于新集合的配置名。省略这个参数将导致配置名字为默认的“configuration1” 

* -DnumShards=2 我们打算将索引分割到逻辑分区的个数。 

浏览 http://localhost:8983/solr/#/~cloud 地址，看看集群的状态。你从 zookeeper 浏览程序能看到 Solr 配置文件被加载成”myconf”，同时一个新的叫做”collection1”的文档集合被创建。collection1 下是碎片列表，这些碎片组成了那完整的集合。 

现在我们想要启动我们的第二个服务器；因为我们没明确设置碎片 id，它将自动被设置成 shard2，启动第二个服务器，指向集群 

```
cd example2 

java -Djetty.port=7574 -DzkHost=localhost:9983 -jar start.jar 
```

* -Djetty.port=7574 只是一种用于让 Jetty servlet 容器使用不同端口的方法 

* -DzkHost=localhost:9983 指向全体包含集群状态的 Zookeeper 服务.这个例子中，我们运行 的是一个内嵌在第一个 solr 服务器中的独立的 Zookeeper 服务.默认情况下，一个内嵌的 Zookeeper 服务运行在 9983 端口上。 

如果你刷新浏览器，你现在在 collection1 下应该看到两个 shard1 和 shard2。接下来，索引一些文档。如果你想要快速完成一些，像 java 你能够使用 CloudSolrServer solrj 的实现和简单的用 Zookeeper 地址初始化它就可以了，或者也可以随便选择某个 solr 实例添加文档，这些文档自动的被导向属于他们的地方。 

```
cd exampledocs 

java -Durl=http://localhost:8983/solr/collection1/update -jar post.jar 

ipod_video.xml 

java -Durl=http://localhost:8983/solr/collection1/update -jar post.jar 

monitor.xml 

java -Durl=http://localhost:8983/solr/collection1/update -jar post.jar 

mem.xml 
```

现在，发起一个获得覆盖整个集合的分布式搜索服务搜索到的任何一个服务的结果的请求： 

```
http://localhost:8983/solr/collection1/select?q=*:*
```

如果在任一个点你希望重新加载或尝试不同的配置,你能通过关闭服务器后简单的删除 solr/zoo_data 目录方式删除所有 zookeeper 的云状态。 

**3.3 简单的支持副本的两个碎片集群** 

有先前的例子，这个例子通过再创建 shard1 和 shard2 副本很容易创建起来。额外的 shard 副本用于高可用性和容错性，或用于提高集群的检索能能力。 首先，通过前一个例子， 我们已经有两个碎片和一些文档索引在里面了。然后简单的复制 那两个服务：

```
cp -r example exampleB 

cp -r example2 example2B 
```

然后在不同的端口下启动这两个新的服务，每个都在各自的视窗下执行 

```
cd exampleB 

java -Djetty.port=8900 -DzkHost=localhost:9983 -jar start.jar 

cd example2B 

java -Djetty.port=7500 -DzkHost=localhost:9983 -jar start.jar 
```

刷新 zookeeper 浏览页面，核实 4 个 solr 节点已经启动，并且每个 shard 有两个副本，因为我们已经告诉 Solr 我们想要 2 个逻辑碎片，启动的实例 3 和 4 自动的被赋值为两个 shards 的副本。 

现在，向任何一个服务器发送请求来查询这个集群 

```
http://localhost:7500/solr/collection1/select?q=*:* 
```

多次发送这个请求，来研究 solr 服务的日志。你应该能够发现 Solr 实现跨副本的负载均衡请求，用不同的服务满足每一个请求。浏览器发送请求的服务器里有一个顶层请求的日志描述，也会有被合并起来生成完成响应的子请求的日志描述。 

为了证明故障切换的可用性，在任何一个服务器（除了运行 Zookeeper 的服务器）运行窗口下按下 CTRL-C。一旦那个服务实例停止运行，就向剩余运行的服务器的任何一个发送另一个查询请求，你应该仍然看到完整的结果。 

SolrCloud 能够持续提供完整查询服务直到最后一个维护每一个碎片的服务器宕机。你能通过明确地关闭各个实例和查询结果来证明这种高可用性。如果你关掉维护某一个碎片的所有服务器，请求其他服务器时将会返回 503 错误结果。要想从依然运行的碎片服务中返回可用的正确文档，需要添加 shards.tolerant=true 查询参数。 

SolrCloud 用多主服务+监控者服务实现。这意味着某写节点或副本扮演一种特殊角色。你不需要担忧你关掉的是主服务或是集群监控，如果你关掉它们中的一个，故障切换功能将自动选出新的主服务或新的监控服务，新的服务将无缝接管各自的工作。任何 Solr 实例都能被升级为这些角色中的某个角色。

**3.4 支持副本和完整 zookeeper 服务的两个碎片集群** 

例 B 的问题是可以有足够多的服务器保证，任何一个损坏的服务器的信息幸存下来，但却只有一个包含集群状态的 zookeeper 服务器。如果那台 zookeeper 服务器损坏，分布式查询将仍然工作在上次 zookeeper 保存的集群状态下，可能没有机会改变集群的状态了。

在 a zookeeper ensemble(zookeeper 集群)中运行多个 zookeeper 服务器保证 zookeeper 服务的高可用性。每个 zookeeper 服务器需要知道集群中其他服务器，且这些服务器中的一个主服务器需要提供服务。例如： 3 台组成的 zookeeper 集群允许任一个故障，而后通过剩余 2 个协商一个主服务继续提供服务。5 台的需要允许一次 2 个服务器故障。

对于用于生产服务时，推荐运行一个额外的 zookeeper 集群而不是运行内嵌在 Solr 里面的 zookeeper 服务。对于这个例子，为了简单点我们将用内嵌的 zookeeper 服务。

首先停止 4 个服务器并清理 zookeeper 数据 

```
rm -r example*/solr/zoo_data 
```

我们将分别在 8983，7574，8900，7500 端口运行服务。默认的方式是在主机端口+1000 运行内嵌的 zookeeper 服务，所以如果我们用前 3 个服务器运行内嵌 zookeeper，地址分别是 localhost:9983，localhost:8574，localhost:9900。 

为了方便，我们上传第一个服务器 solr 配置到集群。你将注意到第一个服务器阻塞了，直到你启动了第二个服务器。这是因为 zookeeper 需要达到足够的服务器数目才允许操作。 

```
cd example 

java -Dbootstrap_confdir=./solr/collection1/conf -Dcollection.configName=myconf -DzkRun -DzkHost=localhost:9983,localhost:8574,localhost:9900 -DnumShards=2 -jar start.jar 

cd example2 

java -Djetty.port=7574 -DzkRun -DzkHost=localhost:9983,localhost:8574,localhost:9900 -jar start.jar 

cd exampleB 

java -Djetty.port=8900 -DzkRun -DzkHost=localhost:9983,localhost:8574,localhost:9900 -jar start.jar 

cd example2B 

java -Djetty.port=7500 -DzkHost=localhost:9983,localhost:8574,localhost:9900 -jar start.jar 
```

现在我们已经运行起由 3 个内嵌 zookeeper 服务组成的集群，尽管某个服务丢失依然保持工作。通过 CTRL+C 杀掉例 B 的服务器然后刷新浏览器看 zookeeper 状态可以断定 zookeeper 服务仍然工作。

* **注意**：当运行在多个 host 上时，在每个需要设置对应的 hostname，-DzkRun=hostname:port，-DzkHosts 使用准确的 host 名字和端口，否则默认的 localhost 将不能工作。 

### **4.ZooKeeper** 

为了容错和高可用多个 zookeeper 服务器一起运行，这种模式叫做 an ensemble。对于生产环境，推荐额外运行 zookeeper ensemble 来代替 solr 内嵌的 zookeeper 服务（关于下载和运行 ensemble 的更多信息访问 Apache ZooKeeper）。更特殊的是，试着启动和管理 zookeeper。它的运行实际上非常简单。你很容易让 Solr 运行 Zookeeper，但是记住一个 zookeeper 集群不容易进行动态修改。除非将来支持动态修改，否则最好在回滚重启时进行修改。与 Solr 分开处理通常是最可取的。 

当 Solr 运行内嵌 zookeeper 服务时，默认使用 solr 端口+1000 作为客户端口，另外，zookeeper 客户端口+1 作为 zookeeper 服务端口， zookeeper 客户端口+2 作为主服务选举端口。所以第一个例子中，Solr 运行在 8983 端口，内嵌 zookeeper 使用 9983 作为客户端端口，9984 和 9985 作为服务端口。 

鉴于确保 Zookeeper 搭建起来是非常快速的，要记住几点：

* Solr 不强烈要求使用 Zookeeper，很多情况优化可能不是必须的。
* 当添加更多的 Zookeeper 节点对读数据性能会有帮助，但也轻微的降低了写的性能。
* 当集群处于稳定运行时，solr 与 Zookeeper 没有太多的交互。

如果你需要优化 zookeeper，这里有几个有益的要点： 

* ZooKeeper 在专用的机器上运行效果最好。ZooKeeper 提供的是一种及时服务，专用设备有助于确保及时服务的响应。当然，专用机器并不是必须的。 

* ZooKeeper 的事务日志和快照在不同的硬盘驱动器上工作效果最好 

* 如果配置支持 solr 的 Zookeeper，各自使用独立的硬盘驱动器有助于性能。

### **5.通过集合api管理集合** 

你可以通过集合 API 管理集合。在这个 API 壳下，一般使用 CoreAdmin API 异步管理每台服务器上的 SolrCores，对于如果你自己处理便独立的 CoreAdmin API 通过替换 action 参数就能调用每个服务器来说，这无疑是一个必不可少好东西。 

**5.1 创建接口** 

```
http://localhost:8983/solr/admin/collections?action=CREATE&name=mycollection&numShards=3&replicationFactor=4 
```

**相关参数：** 

* **name：**将被创建的集合的名字
* **numShards：**集合创建时需要创建逻辑碎片的个数
* **replicationFactor：**每个文档副本数。replicationFactor(复制因子)为 3 意思是每个逻辑碎片将有 3 份副本。注：Solr4.0 中，replicationFactor是 additional * 副本数 ，而不是副本总数
* **maxShardsPerNode：**一个创建操作将展开创建
* **numShards*replicationFactor：** 碎片副本遍布在你的 Solr 节点上，公平分布，同一个碎片的两个副本不会在同一个 Solr 节点上。如果创建操作完成时 Solr 损坏，该操作不会创建出新集合的任何部分。该参数用来防止在同一个 Solr 节点创建太多副本，默认参数 1.如果它的值与整体集合中numShards*replicationFactor 副本数分布到正常活跃的 Solr 节点的数不符，将不能创建任何东西
* **createNodeSet：**如果不提供该参数，创建操作将创建碎片副本展开分布到所有活跃的 Solr 节点上。提供该参数改变用于创建碎片副本的节点集合。参数值的格式是："<node-name1>,<node-name2>,...,<node-nameN>"，

```
createNodeSet=localhost:8983_solr,localhost:8984_solr,localhost:8985_solr
```

* **collection.configName**:用于新集合的配置文件的名称。如果不提供该参数将使用集合名称作为配置文件的名称。

**Solr4.2 相关参数：** 

**name：**将被创建的集合别名的名字 

**collections：**逗号分隔的一个或多个集合别名的列表 

**5.2 删除接口** 

```
http://localhost:8983/solr/admin/collections?action=DELETE&name=mycollection
```

**相关参数：** 

**name：**将被删除的集合的名字 

**5.3 重新加载接口** 

```
http://localhost:8983/solr/admin/collections?action=RELOAD&name=mycollection 
```

**相关参数：** 

**name：**将被重载的集合的名字 

**5.4 分割碎片接口** 

```
http://localhost:8983/solr/admin/collections?action=SPLITSHARD&collection=<collection_name>&shard=shardId 
```

**Solr4.3 相关参数：** 

**collection：**集合的名字 

**shard：**将被分割的碎片 ID 

这个命令不能用于使用自定义哈希的集群，因为这样的集群没有一个明确的哈希范围。 它只用于具有 plain 或 compositeid 路由的集群。 

该命令将分割给定的碎片索引对应的那个碎片成两个新碎片。通过将碎片范围划分成两个相等的分区和根据新碎片范围分割出它在父碎片(被分的碎片)中的文档。新碎片将被命名为 appending_0 和_1。例如：shard=shard1 被分割，新的碎片将被命名为 shard1_0 和 shard1_1。一旦新碎片被创建，它们就被激活同时父碎片（被分的碎片）被暂停因此将没有新的请求到父碎片(被分的碎片)。 该特征达到了无缝分割和无故障时间的要求。原来的碎片数据不会被删除。使用新 API 命令重载碎片用户自己决定。该特性发布始于 Solr4.3，由于 4.3 发布版本发现了一些 bugs,所以要使用该特性推荐等待 4.3.1。 

### **6.集合别名** 

别名允许你创建独立的指向一个或多个真是集合的虚拟集合，你能够在运行时修改别名。 

**6.1 创建别名**

```
http://localhost:8983/solr/admin/collections?action=CREATEALIAS&name=alias&collections=collection1,collection2,… 
```

创建或修改别名。用于发送修改的别名应该只映射一个独立的集合。读的别名能映射一个或多个集合。 

**6.2 移除存在的别名** 

```
http://localhost:8983/solr/admin/collections?action=DELETEALIAS&name=alias 
```

### **7.经CoreAdmin创建 cores** 

经过 CoreAdmin 可以创建新的 Solrcores 和使新的 Solrcores 与一个集合关联，创建时附加云相关参数 ：

* **collection：** 这个 core 所属的集合的名字。默认 core 的名字 

* **shard：** 这个 core 代表的碎片的 id(可选-通常自动设置一个碎片 id) 

* **numShards：**集合碎片个数。该参数只在集合第一个 core 创建时有用 

* **collection：**<param>=<value> 集合创建时设置属性值，用 collection.configName=<configname> 指明新集合的配置文件 

```
//curl 

'http://localhost:8983/solr/admin/cores?action=CREATE&name=mycore&collection=collection1&shard=shard2' 
```

### **8.分布式请求** 

查询一个集合的所有碎片(注意:那个集合已经隐含在 URL 中了) 

```
http://localhost:8983/solr/collection1/select? 
```

查询一个兼容集合的所有碎片

```
http://localhost:8983/solr/collection1/select?collection=collection1_recent
```

查询多个兼容集合的所有碎片

```
http://localhost:8983/solr/collection1/select?collection=collection1_NY,collection1_NJ,collection1_CT 
```

查询集合的某些碎片。

```
//例子中，用户已经按日期对索引进行分割了，每月创建一个新的碎片

http://localhost:8983/solr/collection1/select?shards=shard_200812,shard_200912,shard_201001 
```

明确指明以想要查询碎片的地址 

```
http://localhost:8983/solr/collection1/select?shards=localhost:8983/solr,localhost:7574/solr 
```

明确指明以想要查询碎片的地址，同时为负载均衡和故障切换给出可选的地址 

```
http://localhost:8983/solr/collection1/select?shards=localhost:8983/solr|localhost:8900/solr,localhost:7574/solr|localhost:7500/solr 
```

### **9.必须的配置** 

所有必须的配置已经安装在 Solr 的例子中。下面是如果你正在迁移旧的配置文件你需要添加什么，或不应该删除什么 

**9.1 schema.xml** 

字段_version_是必须定义的

```
<field name="_version_" type="long" indexed="true" stored="true" 
multiValued="false"/> 
```

**9.2 solrconfig.xml** 

必要有一个更新日志的定义，这个应该被定义在 updateHandler 标签中 

```
<!-- Enables a transaction log, currently used for real-time get."dir" - the target directory for transaction logs, defaults to thesolr data directory. --> 

<updateLog> 

<str name="dir">${solr.data.dir:}</str> 

</updateLog> 
```

必须有一个复制处理调用的定义

```
<requestHandler name="/replication" class="solr.ReplicationHandler" startup="lazy" /> 
```

必须有一个获取实际时间处理的调用的定义 

```
<requestHandler name="/get" class="solr.RealTimeGetHandler"> 

<lst name="defaults"> 

<str name="omitHeader">true</**str**> 

</lst> 

</requestHandler> 
```

必须有管理句柄的定义 

```
<requestHandler name="/admin/" class="solr.admin.AdminHandlers"/> 
```

DistributedUpdateProcessor 是默认更新链的部分也会被自动注入自定义更新链。 

```
<updateRequestProcessorChain name="sample"> 

<processor class="solr.LogUpdateProcessorFactory" /> 

<processor class="solr.DistributedUpdateProcessorFactory"/> 

<processor class="my.package.UpdateFactory"/> 

<processor class="solr.RunUpdateProcessorFactory" /> 

</updateRequestProcessorChain> 
```

如果不想让它自动注入到你的链中(说明你想使用 SolrCloud 功能，但想自己分发修改)那么在你的链中指明下面的更新处理工厂: NoOpDistributingUpdateProcessorFactory 

**9.3 solr.xml** 

必须保留 admin path 作为默认 core 

```
<cores adminPath="/admin/cores"/> 
```

### **10.可变集群** 

当你在一个集合中启动 SolrCore 时通过 numShards 参数可以控制集群的规模。 这个参数一般自动赋给关联每个实例的碎片。启动 numShards 个实例后所有的 SolrCores 都被均匀的加入每个碎片作为副本。

向集合添加 SolrCores 保证其启动是很简单的，你随时都可以进行这样的操作。新的SolrCore 在激活前会把它的数据同步到碎片中的副本上。 

如果你要在几台机器上启动集群，不仅添加副本需要花时间扩展成集群也需要时间，那么，你可以选择通过每部机器负责几个碎片的方式启动(使用多 SolrCores)，然后通过对碎片启动副本的形式迁移碎片到新的设备上，最终移除原来设备上的碎片。 

Solr4.3 具备新的 API 接口， SPLITSHARD，分割已存在的碎片成两份新的碎片，相关介绍前面讲过。 

### **11.近实时搜索** 

如果你希望启用近实时搜索，你可能需要修改放在 ZooKeeper 中的 solrconfig.xml 文件，添加启动软件自动提交配置，或者通过向集群发送软提交。详见[近实时搜索](https://cwiki.apache.org/confluence/display/solr/NearRealtimeSearch)。 

### **12.参数参考介绍** 

**12.1 集群参数** 

| 参数      | 默认值 | 说明                                                         |
| --------- | ------ | ------------------------------------------------------------ |
| numShards | 1      | 文档被散列存储到所有碎片的数量。每个碎片有一个主碎片(领导者),每个主碎片有 N 个副本 |

**12.2 Solr云实例参数** 

参数在 solr.xml 中设置,实例的运行与系统环境变量也有关。重要事项：hostPort 是每个Solr 实例运行的端口，zookeeper 通过该端口通知集群的状态(剩余等)，默认值是 8983。solr.xml 例子用的是 jetty.port 环境变量,所以如果你不想用 8983，那么你启动 Solr 前设置 jetty.port 变量，或修改 solr.xml 文件。

| 参数     | 默认值               | 说明                                                         |
| -------- | -------------------- | ------------------------------------------------------------ |
| host     | 找到的第一个本地地址 | 如果自动找到的地址是错的，你可以通过这个参数修改             |
| hostPort | jettry 的系统配置    | 这个参数是 Solr 使用的，默认使用的是 jettry 的配置文件       |
| host     | solr                 | Solr 部署成 web 应用时的上下文路径。（注：在 Solr4.0 中，强制要求 hostContext 不能包含”/”或”_”字符。Solr4.1 开始，没有这个限制了，并且建议以”/”开头。运行 jetty 例子时，hostContext 既是jetty 容器的 servlet 上下文地址也是 SolrCloud 上下文地址，例：-DhostContext=/solr） |

**12.3 Zookeeper** **参数** 

| 参数            | 默认值                  | 说明                                                         |
| --------------- | ----------------------- | ------------------------------------------------------------ |
| zkRun           | localhost:solrPort+1000 | 配置该参数让 Solr 运行起内嵌的 Zookeeper 服务。参数设置了 Zookeeper 所在的服务器地址。这个值会出现在 zkHost 参数中，我们能够知道都有哪些 zk 服务器。简单模式可以使用默认值；注意：这个值必须是 zkHost 中的一个；另外，多服务器版的不能使用默认的地址。 |
| zkHost          | 无                      | ZooKeeper host 地址——你的 ZooKeeper 集群一般节点地址使用逗号分割 |
| zkClientTimeout | 15000                   | session 没有过期前提下，不和 ZooKeeper交互的最长间隔，zkClientTimeout 参数在 solr.xml 设置，也可以通过系统参数设置。 |

**12.4 云核参数** 

| 参数  | 默认值                             | 说明                                                         |
| ----- | ---------------------------------- | ------------------------------------------------------------ |
| shard | shard 的 ID基于 numShards 自动分配 | 允许你指定 SolrCores 里的 shard 的 Id，碎片 id 可以在每个 core 元素的属性中配置 |

### **13.把配置文件给 ZooKeeper** 

**13.1 配置启动引导参数** 

第一次启动 Solr，有两种使用系统参数加载初始化 Zookeeper 配置文件的方法。记住，只用于第一次启动或覆盖配置文件时。每次启动都要带着系统参数，系统参数会覆盖配置文件中名字相同的值。 

* 看 solr.xml 文件，找到每个 SolrCore 加载的 conf 目录。 ‘config set’ (配置集)的名字就是 SolrCore 的集合的名字，集合使用与它名字相同的配置集合。 

| 参数           | 默认值 | 说明                                                         |
| -------------- | ------ | ------------------------------------------------------------ |
| bootstrap_conf | 无     | 如果启动时携带参数 -Dbootstrap_conf=true，每个 SolrCore 将把自己的配置文件加载关联到它对应的集合上。 |

* 通过给的配置文件名字从指定目录下加载配置集。尽管没有指定集合到配置集的关联，如果只有一个配置集，集合将自动链接到这个配置集。 

| 参数                  | 默认值         | 说明                                                         |
| --------------------- | -------------- | ------------------------------------------------------------ |
| bootstrap_confdir     | 无             | 如果你通过 -bootstrap_confdir=<目录≶ 启动，指定的配置目录文件将会被提交到 ZooKeeper，设置配置名可以用下面的参数：collection.configName |
| collection.configName | configuration1 | 通过 bootstrap_confdir 设置配置集的名称                      |

**13.2 命令行工具** 

CLI 工具也可以配置 Zookeeper。 它与上面两种方法一样。 它提供一些命令，能够配置集到集合关联，创建 Zookeeper 路径或移除，还能从 zookeeper 下载配置文件到本地。 

```
usage: ZkCLI 

-c,--collection <arg> for linkconfig: name of the collection 

-cmd <arg> cmd to run: bootstrap, upconfig, downconfig,linkconfig, 

makepath, clear 

-d,--confdir <arg> for upconfig: a directory of configuration files 

-h,--help bring up this help page 

-n,--confname <arg> for upconfig, linkconfig: name of the config set 

-r,--runzk <arg> run zk internally by passing the solr run port -only for 

clusters on one machine (tests, dev) 

-s,--solrhome <arg> for bootstrap, runzk: solrhome location 

-z,--zkhost <arg> ZooKeeper host address 
```

**13.3 例子** 

```
# try uploading a conf dir 

java -classpath example/solr-webapp/WEB-INF/lib/* 

org.apache.solr.cloud.ZkCLI -cmd upconfig -zkhost 127.0.0.1:9983 -confdir 

example/solr/collection1/conf -confname conf1 

\# try linking a collection to a conf set 

java -classpath example/solr-webapp/WEB-INF/lib/* 

org.apache.solr.cloud.ZkCLI -cmd linkconfig -zkhost 127.0.0.1:9983 - 

collection collection1 -confname conf1 

\# try bootstrapping all the conf dirs in solr.xml 

java -classpath example/solr-webapp/WEB-INF/lib/* 

org.apache.solr.cloud.ZkCLI -cmd bootstrap -zkhost 127.0.0.1:9983 - 

solrhome example/solr 
```

**13.4 脚本** 

如果你不想使用 jettry 容器，example/cloud-scripts 目录下有系统脚本文件，它帮你处理了 classpath 和 class name，命令变成为： 

```
sh zkcli.sh -cmd linkconfig -zkhost 127.0.0.1:9983 -collection 

collection1 -confname conf1
```

### **14.ZooKeeper chroot** 

如果其他应用已经在用 Zookeeper，你想保持原应用的管理方式，又或者有多个分开的 solrcloud 集群共用一个 zookeeper，你可以用 zookeeper 的‘chroot’选项。

> An optional "chroot" suffix may also be appended to the connection string. This will run the client commands while interpreting all paths relative to this root (similar to the unix chroot command). If used the example would look like: "127.0.0.1:4545/app/a" or "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002/app/a" where the client would be rooted at "/app/a" and all paths would be relative to this root - ie getting/setting/etc... "/foo/bar" would result in operations being run on "/app/a/foo/bar" (from the server perspective). 

要使用这个特点，zkHost 参数用上”chroot”后缀，而后简单启动 solr 

```
java -DzkHost=localhost:9983/foo/bar -jar start.jar 
```

或者 

```
java -DzkHost=zoo1:9983,zoo2:9983,zoo3:9983/foo/bar -jar start.jar 
```

* **提示**：Solr4.0 启动时你需要预先创建 ZooKeeper 路径，Solr4.1 以后再使用 eitherbootstrap_conf 或 boostrap_confdir 的路径是自动初始化的了。 

### **15.已知的局限性** 

很少 Solr 搜索框架不支持分布式搜索的。有些情况下，一个框架无法支持分布式，不过，那只是时间和努力的问题。不支持标准分布式搜索的搜索框架，它的 SolrCloud 同样不支持。通过 distrib=false 控制是否使用分布式。 

组群特点只能是在同一个存储碎片上的组群。必须自定义碎片特征才能使用组群特点。 

如果要更新支持 SolrCloud 的 Solr4.0 到 4.1，注意 name_node 参数的定义方式已经改变了。这导致一种情况发生，由于 name_node 用 ip 地址代替了服务器的名字，使 SolrCloud 无法感知到这个 name_node。你可以通过 solr.xml 配置 ip 到服务器名的关联解决问题。 

### **16.术语**

| 术语       | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| Collection | A single search index                                        |
| Shard      | A logical section of a single collection (also called Slice). Sometimes  people will talk about “Shard” in a physical sense (a manifestation of a logical shard) |
| Replica    | A physical manifestation of a logical Shard, implemented as a single  Lucene index on a SolrCore |
| Leader     | One Replica of every Shard will be designated as a Leader to coordinate indexing for that Shard |
| SolrCore   | Encapsulates a single physical index. One or more make up logical shards  (or slices) which make up a collection |
| Node       | A single instance of Solr. A single Solr instance can have multiple |
| Cluster    | All of the nodes you are using to host SolrCores             |

[英文原文地址](https://lucene.apache.org/solr/guide/6_6/solrcloud.html)

[SolrCloud使用教程和原理介绍](https://blog.csdn.net/wulantian/article/details/38539923)

[Solr索引数据同步ReplicationHandler](https://my.oschina.net/baowenke/blog/103146)

[Solr 实现 并集式、多值、复杂 过滤查询（filterQuery）](https://www.cnblogs.com/seaspring/p/6428074.html)

[Solr 对比 Lucene 的优势](https://blog.csdn.net/zhou_shaowei/article/details/61205537)

[Solr4.0(SolrCloud) & ElasticSearch(ES) 比较](https://blog.csdn.net/porui/article/details/7703856)

## 参考：

[随笔分类 - 搜索引擎](https://www.cnblogs.com/leeSmall/category/1210814.html)