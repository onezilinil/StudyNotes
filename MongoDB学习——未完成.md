# MongoDB学习

## 一、MongoDB介绍

### 1. MongoDB是什么

MongoDB是一个开源文档数据库，提供高性能、高可用和自动扩展的功能。是使用C++编写的非关系型数据库。

### 2. 为什么用MongoDB

* MongoDB 性能高
  * MongoDB 是一个开源文档数据库，是用 C++ 语言编写的非关系型数据库。其特点是高性能、高可用、可伸缩、易部署、易使用，存储数据十分方便，主要特性有：**面向集合存储，易于存储对象类型的数据，模式自由，支持动态查询，支持完全索引，支持复制和故障恢复，使用高效的二进制数据存储，文件存储格式为 BSON** （ 一种 JSON 的扩展）等。
  * MongoDB 提供高性能数据读写功能，并且性能还在不断地提升。根据官方提供的 MongoDB 3.0 性能测试报告，在 YCSB 测试中，MongoDB 3.0 在多线程、批量插入场景下的处理速度比 MongoDB 2.6 快 7 倍。
* MongoDB 支持分布式

  * 在生产过程中，因机器故障导致系统宕机的问题不可避免；集中式系统在计算能力和存储能力方面的瓶颈，也无法满足当前的数据量爆发式增长的需求。这两个问题就是系统对高可用和可伸缩架构的需求，MongoDB 在原生上就可满足这两方面的需求。
  * MongoDB 的高可用性体现在对副本集 Replication 的支持上，可伸缩性体现在分片集群的部署方式上。
  * 主从关系（Master-Slaver） 负责数据的同步和读写分离；Arbiter 服务负责心跳（Heartbeat）监控，Master 宕机时可将 Slaver 切换到 Master状态，继续提供数据的服务，完成了数据的高可用需求。
  * MongoDB 提供水平可伸缩性功能的是分片（Shard）。分片与在 HDFS 分布式文件系统中上传文件会将文件切成 128MB（Hadoop2.x 默认配置）相似，通过将数据切成数片（Sharding）写入不同的分片节点，完成分布式写的操作。同时，MongoDB 在读取时提供了分布式读的操作，这个功能与 HDFS 的分布式读写十分类似。
* MongoDB 安装和部署容易
  * MongoDB 的安装十分友好，部署容易，支持多种安装方式，对第三方组件的依赖很低，用户可以使用它较容易地搭建起一个完整的生产集群。MongoDB 的单机部署十分简单，针对分片副本集安装也有第三方工具提供辅助。

* MongoDB 便于开发
  * MongoDB 对开发者十分友好，便于使用。支持丰富的查询语言、数据聚合、文本搜索和地理空间查询，用户可以创建丰富的索引来提升查询速度，MongoDB 被称为最像关系数据库的非关系数据库。
  * MongoDB 允许用户在服务端执行脚本，可以用 script 编写某个函数，直接在服务端执行，也可以把函数的定义存储在服务端，使用时直接调用即可。MongoDB 支持各种编程语言，包括 Ruby、、Java、C++、、 等。

### 3. 传统数据库和MongoDB

* 传统文档数据库
  * 传统的文档数据库（Document Storage）概念的提岀要追溯到 1989 年，Lotus 提出的 Notes 产品被称为文档数据库，这种文档数据库常用于管理文档，如 Word、建立工作流任务等。
  * **文档数据库区别于传统的其他数据库，它可用来管理文档，尤其擅长处理各种非结构化的文档数据。在传统的数据库中，信息被分割成离散的数据段，而在文档数据库中，文档是处理信息的基本单位。**
  * 传统的文档数据库与 20 世纪 50 ~ 60 年代管理数据的文件系统不同，文档数据库仍属于数据库范畴。首先，文件系统中的文件基本上对应于某个应用程序。当不同的应用程序所需要的数据部分相同时，也必须建立各自的文件，而不能共享数据，而**文档数据库可以共享相同的数据。因此，文件系统比文档数据库数据冗余度更大，更浪费存储空间，且更难于管理维护。**其次，文件系统中的文件是为某一特定应用服务的，因此，要想对现有的数据再增加一些新的应用是很困难的，系统难以扩展，数据和程序缺乏独立性。而文档数据库具有数据的物理独立性和逻辑独立性，数据和程序分离。 

* MongoDB
  *  NoSQL 中的文档数据库（以下文档数据库均指 NoSQL 中的文档数据库）与传统的文档数据库不是同一种产品，NoSQL 中的文档数据库**MongoDB有自己特定的数据存储结构及操作要求**。
  * 在传统数据库的发展过程中，基本都是出现一种数据模型，再依据数据模型，开发出相关的数据库，例如，层次数据库是建立在层次数据模型的基础上，关系数据库是建立在关系数据模型的基础上的。NoSQL 中文档数据库的出现也是建立在文档数据模型的基础上的。
  * **NoSQL 中的文档数据库与传统的关系数据库均建立在对磁盘读写的基础上，实现对数据的各种操作。文档数据库的设计思路是尽可能地提升数据的读写性能，为此选择性地保留了部分关系型数据库的约束，通过减少读写过程的规则约束，提升了读写性能**。 

### 4. MongoDB文档数据模型

**传统的关系型数据库需要对表结构进行预先定义和严格的要求，而这样的严格要求，导致了处理数据的过程更加烦琐，甚至降低了执行效率。**在数据量达到一定规模的情况下，传统关系型数据库反应迟钝，想解决这个问题就需要反其道而行之，尽可能去掉传统关系型数据库的各种规范约束，甚至事先无须定义数据存储结构。 

**文档存储支持对结构化数据的访问，与关系模型不同的是，文档存储没有强制的架构。文档存储以封包键值对的方式进行存储，文档存储模型支持嵌套结构。**例如，文档存储模型支持 XML 和 JSON 文档，字段的“值”可以嵌套存储其他文档，也可存储数组等复杂数据类型。**MongoDB 存储的数据类型为 BSON，BSON 与 JSON 比较相似，文档存储模型也支持数组和键值对。** 

![文档的数据结构](F:\学习笔记\MongoDB\文档的数据结构.png)

文档数据库无须事先定义数据存储结构，这与键值数据库和列族数据库类似，只需在存储时采用指定的文档结构即可。从上图可以看出，**一个`{}`中包含了若干个键值对，大括号中的内容就被称为一条文档。** 

### 5. BSON是什么

 MongoDB 所采用的数据格式被称为 BSON（Binary JSON），是一种基于 JSON 的二进制序列化格式，用于 MongoDB 存储文档并进行远程过程调用。 

>  JSON 是一种网络常用的数据格式，具有自描述性。JSON 的数据表示方式易于解析，但支持的数据类型有限。BSON 目前主要用于 MongoDB 中，选择 JSON 进行改造的原因主要是 JSON 的通用性及 JSON 的 schemaless （ 不对其中存储的数据执行任何操作 ）的特性。 

BSON的特性：

* 更快的遍历速度
  * BSON 对 JSON 的一个主要的改进是，**在 BSON 元素的头部有一个区域用来存储元素的长度**（我理解为数组一样）， 当遍历时，如果想跳过某个文档进行读取，就可以先读取存储在 BSON 元素头部的元素的长度， 直接 seek 到指定的点上就完成了文档的跳过。
    在 JSON 中，要跳过一个文档进行数据读取，需要在对此文档进行扫描的同时匹配才可以完成跳过操作。

* 操作更简易
  * 如果要修改 JSON 中的一个值，如将 9 修改为 10，这实际是将一个字符变成了两个，会导致其后面的所有内容都向后移一位。
    在 BSON 中，可以指定这个列为整型，那么，当将 9 修正为 10 时，只是在整型范围内将数字进行修改，数据总长不会变化。
  *  注意：
    * 如果数字从整型增大到长整型，还是会导致数据总长增加。
    *  有的时候，**BSON相对JSON来说也并没有空间上的优势**，比如对{“field”:7}，在JSON的存储上7只使用了一个字节，而如果用BSON，那就是至少4个字节（32位）。

* 支持更多的数据类型
  * BSON 在 JSON 的基础上增加了很多额外的类型，BSON 增加了“byte array”数据类型。这使得二进制的存储不再需要先进行 base64 转换再存为 JSON，减少了计算开销。

BSON的数据类型（加粗的为JSON支持的）

| 类型/结构          | 描述示例                                                     |
| ------------------ | ------------------------------------------------------------ |
| **NULL**           | 表示空值或者不存在的字段，{"x" : null}                       |
| **Boolean**        | 布尔型有 true 和 false，{"x" : true}                         |
| **Number**         | 数值：客户端默认使用 64 位浮点型数值。{"x" : 3.14} 或 {"x" : 3}。对于整型值，包括 NumberInt（4 字节符号整数）或 NumberLong（8 字节符号整数），用户可以指定数值类型，{"x" : NumberInt("3")} |
| **String**         | 字符串：BSON 字符串是 UTF-8，{"x" : "中文"}                  |
| Regular Expression | 正则表达式：语法与 [JavaScript](http://c.biancheng.net/js/) 的正则表达式相同，{"x" : /[cba]/} |
| **Array**          | 数组：使用“[]”表示，{"x" : ["a", "b", "c"]}                  |
| **Object**         | 内嵌文档：文档的值是嵌套文档，{"a" : {"b" : 3}}              |
| ObjectId           | 对象 id：对象 id 是一个 12 字节的字符串，是文档的唯一标识，{"x" : objectId()} |
| BinaryData         | 二进制数据：二进制数据是一个任意字节的字符串。它不能直接在 Shell 中使用。如果要将非 UTF-8 字符保存到数据库中，二进制数据是唯一的方式 |
| JavaScript         | 代码：查询和文档中可以包括任何 JavaScript 代码，{"x" : function(){/*...*/}} |
| Data               | 日期：{"x" : new Date()}                                     |
| Timestamp          | 时间戳：var a = new Timestamp()                              |

[BSON的介绍及BSON与JSON的区别]( https://blog.csdn.net/m0_38110132/article/details/77716792 )

## 二、MongoDB使用

### 1. MongoDB数据库的使用

数据库的整体结构组成：**键值对--》文档--》集合--》数据库**

#### 1.1 命名规则

* 由于数据库名称在 MongoDB 中不区分大小写，因此数据库名称不能仅仅区别于字符。 

* 不得使用保留的数据库名，如：admin（ 权限数据库，添加用户到该数据库中，该用户会自动继承数据库的所有权限）、local（ 数据库中的数据永远不会被复制 ）、config （ 分片时，config 数据库在内部使用，保存分子信息 ）。

* 不得有特殊字符 ：/、\、“、$、*、＜ ＞、：、|、? 。 

#### 1.2 创建数据库

```
use myDB
```

MongoDB 使用 use 命令创建数据库，如果数据库不存在，MongoDB 会在第一次使用该数据库时创建数据库。如果数据库已经存在则连接数据库，然后可以在该数据库进行各种操作。 

#### 1.3 查看数据库

```
>show dbs        //可以在任意当前数据库上执行该命令
admin 0.000GB    //保留数据库，admin
myDB 0.000GB     //自定义数据库，myDB
local 0.000GB    //保留数据库，local
test 0.000GB     //保留数据库，test
```

MongoDB 默认的数据库为 test，如果没有创建新的数据库，集合将存储在 test 数据库中。如果自定义数据库没有插入记录，则用户在查看数据库时是不会显示的，**只有插入数据的数据库才会显示相应的信息**。 

#### 1.4 获取数据库信息

```
>use test              //选择执行的test数据库
switched to db test    //use执行后返回的结果
> db.stats ()         //统计数据信息
{ 
    "db" : "test",     //数据库名
    "collections" : 0, //集合数量
    "views" : 0,
    "objects" : 0,     //文档数量
    "avgObjSize" : 0,  //平均每个文档的大小
    "dataSize" : 0,    //数据占用空间大小，不包括索引，单位为字节
    "storageSize" : 0, //分配的存储空间
    "nuinExtents" : 0, //连续分配的数据块
    "indexes" : 0,     //索引个数
    "indexsize" : 0,   //索引占用空间大小
    "fileSize" : 0,    //物理存储文件的大小
    "ok" : 1 
}
```

* 注意： 一定要用 use 切换至数据库，否则会出错。

#### 1.5 删除数据库

```
>db.dropDatabase()   //删除当前数据库
{ ndropped" : "myDBn Jok" : 1}    //显示结果删除成功
```

#### 1.6 查看集合

 查询当前数据库下的所有集合

```
>use test
>db.getCollections ()    //查询当前数据下所有的集合名称
```

### 2. MongoDB集合的使用

MongoDB将文档存储在集合中。集合类似于关系数据库中的表。如果集合不存在，则 MongoDB 会在第一次存储该集合数据时创建该集合。 集合名称也区分大小写。

#### 2.1 创建集合

MongoDB集合的创建有显示和隐示两种方式

* 显示创建：
  *  显式创建集合可通过使用`db.createCollection(name, options)`方法来实现，参数 name 指要创建的集合名称，options 是可选项，指定内存大小和索引等，下表描述了 options 可使用的选项。

| 参数   | 类型    | 描述                                                         |
| ------ | ------- | ------------------------------------------------------------ |
| capped | Boolean | （可选）如果为 true，则启用封闭的集合。上限集合是固定大小的集合，它在达到其最大时自动覆盖其最旧的条目。如果指定 true，则还需要指定 size 参数 |
| size   | 数字    | （可选）指定上限集合的最大大小（以字节为单位）。如果 capped 为 true，那么还需要指定此字段的值 |
| max    | 数字    | （可选）指定上限集合中允许的最大文档数                       |

* 注意：插入文档时，MongoDB首先检查集合上限size的大小，然后检查max大小

```
db.createCollection("mySet",{capped: true, size: 6142800, max: 10000})
```

* 隐示创建：
  * 插入文档时，集合不存在则会隐示的自动创建集合

```
db.myDB.insert({"name": "tom"})
```

#### 2.2 查看数据库

```
show collections
```

#### 2.3 修改集合名称

```
db.rename_mySet.renameCollection("rename_mySet")
```

#### 2.4 删除集合

```
db.myDB.drop()
```

### 3. MongoDB文档的使用

 文档是 MongoDB中存储的基本单元，是一组**有序的键值对集合**。 

#### 3.1 命名规则

- 键名区分大小写；

- 键的值区分类型（如字符串和整数等）；

- 键不能重复，在一条文档里起唯一的作用。

*  相同的键值对如果有不同顺序，也是不同的文档 。

```
// 例1：以下两组文档是不同的，因为值的类型不同。
{"recommend":"5"}
{"recommend":5}


// 例2：以下两组文档也是不同的，因为键名是区分大小写的。
{ "Recommend" : " 5 "}
{"recommend":"5"}
```

#### 3.2 插入文档

要将数据插入 MongoDB 集合中，可以使用 MongoDB 的 insert() 方法，同时 MongoDB 针对插入一条还是多条数据，提供了更可靠的 insertOne() 和 insertMany() 方法。

MongoDB 向集合里插入记录时，无须事先对数据存储结构进行定义。如果待插入的集合不存在，则插入操作会默认创建集合。

在 MongoDB 中，插入操作以单个集合为目标，**MongoDB 中的所有写入操作都是单个文档级别的原子操作**。

向集合中插入数据的语法如下： 

```
db.collection名.insert(
<document or array of documents>,
	{
    writeConcern: <document>,    //可选字段
    ordered: <boolean>    //可选字段
    }
)
```

参数说明：

- <document or array of documents> 参数表示可设置插入一条或多条文档。

- writeConcern:<document> 参数表示自定义写出错的级别，是一种出错捕捉机制。

- ordered:<boolean> 是可选的，默认为 true。
  - 如果为 true，在数组中执行文档的有序插入，并且如果其中一个文档发生错误，MongoDB 将返回而不处理数组中的其余文档；
  - 如果为 false，则执行无序插入，若其中一个文档发生错误，则忽略错误，继续处理数组中的其余文档。

（1）不指定_id字段

```
db.myCollection1.insert({item: "card", qty: 15})
```

在插入期间，mongod 将创建 _id 字段并为其分配唯一的 Objectld 值，这里的 mongod 是一个 MongoDB 服务器的实例，也就是 MongoDB 服务驻扎在计算机上的进程。 使用find()方法查看集合中的值

```
db.myCollection1.find()
```

> 不指定_id字段时插入相同文档的时候，也会生成不同ObjectId存储，因为 Objectld 值与执行操作时的机器和时间有关。

（2）指定_id字段

```
db.myCollection1.insert({_id: 10, "item: "card", qty: 16})
```

（3）批量插入

```
db.myCollection1.insert(
    [
        { _id: 11, item: "pencil", qty: 50, type: "no.2" },
        { item: "pen", qty: 20 },
        { item: "eraser", qty: 25 }
    ]
)
```

（4）变量方式插入

```
document = {name: "zs", price: 40}
// 这里面键名有没有双引号都一样，都是字符串
db.myCollection1(document)
```

（5）insertOne() 和 insertMany()

```
db.myCollection1.iusertone( { item: "card", qty: 15 } );


db.myCollection1.insertMany([
    { item: "card", qty: 15 },
    { item: "envelope", qty: 20 },
    { item: "stamps", qty:30 }
]);
```

#### 3.3 修改文档

（1）update方法

基本语法如下：

```
db.collection.update(
    <query>,
    <update>,
    {
        upsert,
        multi,
        writeConcern,
        collation
    }
)
```

参数说明：

- <query＞：参数设置查询条件。

- <update＞：为更新操作符。

- upsert：为布尔型可选项，表示如果不存在 update 的记录，是否插入这个新的文档。true 为插入；默认为 false，不插入。

- multi：也是布尔型可选项，默认是 false，只更新找到的第一条记录。如果为 true，则把按条件查询出来的记录全部更新。

- writeConcem：表示出错级别。

- collation：指定语言。

将item值为card的文档修改：

```
db.myCollection1.update({item: "card"}, {$set: {qty: 15, item: "card1"}})
```

> collation :
>
> collation 特性允许 MongoDB 的用户根据不同的语言定制排序规则，在 MongoDB 中字符串默认当作一个普通的二进制字符串来对比。而对于中文名称，通常有按拼音顺序排序的需求，这时就可以通过collation来实现。 
>
> 创建集合时，指定 collation 为 zh，按 name 字段排序时，则会按照 collation 指定的中文规则来排序，代码如下： 
>
> ```
> db.createCollection ("person", {collation: {locale: "zh" }})    //创建集合并指定语言
> db.person.insert ({name: ”张三”}）
> db.person.insert ({name:"李四”}）
> db.person.insert ({name: ”王五"}）
> db.person.insert ({name: ”马六”}）
> db.person.insert ({name:"张七"})
> db.person.find().sort({name: 1}) //查询并排序
> ```

（2）save方法

基本语法如下：

```
db.collection.save ( obj )
```

obj 代表需要更新的对象，如果集合内部已经存在一个与 obj 相同的“_id”的记录，Mongodb 会把 obj 对象替换为集合内已存在的记录；如果不存在，则会插入 obj 对象。 

```
// 先保存一条数据
db.products.save( { _id: 100, item: "watern, qty: 30 })
// 第二次保存相同id值的时候进行覆盖
db.products.save( { _id : 100, item : "juice" })
```

如果使用 insert 插入记录，若新增数据的主键已经存在，则会抛出 DuplicateKeyException 异常提示主键重复，不保存当前数据。 

（3）findAndModify方法

#### 3.4 删除文档

（1）remove方法

使用remove方法将文档进行完全删除， remove() 函数可以接受一个查询文档作为可选参数来有选择性地删除符合条件的文档。删除文档是永久性的，不能撤销，也不能恢复。因此，在执行 remove() 函数前最好先用 find()命令来查看是否正确。 

基本格式如下：

```
db.collection.remove(
    <query>,
    {
        justOne: <boolean>, writeConcern: <document>
    }
)
```

参数说明：

- query：必选项，是设置删除的文档的条件。

- justOne：布尔型的可选项，默认为false，删除符合条件的所有文档，如果设为 true，则只删除一个文档。

- writeConcem：可选项，设置抛出异常的级别。

```
// 删除item值为card1的文档
db.myCollection.remove({item: "card1"})
```

注意：如果不指定justOne为true的话，可能会删除所有匹配的文档

（2）delete方法

delete和remove不同在于有removeOne方法，不需要参数配置为justOne

```
// 删除第一条匹配查询的文档
db.collection.deleteOne(<query>)
// 删除所有匹配查询的文档
db.collection.deleteMany(<query>)
```

例如：

```
// 删除所有
db.collection.deleteMany ({})
// 删除所有匹配
db.collection.deleteMany ({ status : "A" })
// 删除第一条匹配的文档
db.collection.delete.One ({ status : "D" })
```

#### 3.5 查询文档

MongoDB 中查询文档使用 find() 方法。find() 方法以非结构化的方式来显示所要查询的文档， 查询数据的语法格式如下：

```
db.collection.find(query, projection)
// 返回一条文档
db.collection.find(query, projection)
// 返回结果格式化显示
db.test.find(query, projection).pretty()
```

参数说明：

* query 为可选项，设置查询操作符指定查询条件；

* projection 也为可选项，表示使用投影操作符指定返回的字段，如果忽略此选项则返回所有字段。 

（1）**查询条件**

MongoDB 支持条件操作符，下表为 MongoDB 与 RDBMS 的条件操作符的对比。

| 操作符         | 格式                                                | 实例                                                         | 与 RDBMS where 语句比较                            |
| -------------- | --------------------------------------------------- | ------------------------------------------------------------ | -------------------------------------------------- |
| 等于（=）      | {<key> : {<value>}}                                 | db.test.find( {price : 24} )                                 | where price = 24                                   |
| 大于（>）      | {<key> : {$gt : <value>}}                           | db.test.find( {price : {$gt : 24}} )                         | where price > 24                                   |
| 小于（<）      | {<key> : {$lt : <value>}}                           | db.test.find( {price : {$lt : 24}} )                         | where price < 24                                   |
| 大于等于（>=） | {<key> : {$gte : <value>}}                          | db.test.find( {price : {$gte : 24}} )                        | where price >= 24                                  |
| 小于等于（<=） | {<key> : {$lte : <value>}}                          | db.test.find( {price : {$lte : 24}} )                        | where price <= 24                                  |
| 不等于（!=）   | {<key> : {$ne : <value>}}                           | db.test.find( {price : {$ne : 24}} )                         | where price != 24                                  |
| 与（and）      | {key01 : value01, key02 : value02, ...}             | db.test.find( {name : "《MongoDB 入门教程》", price : 24} )  | where name = "《MongoDB 入门教程》" and price = 24 |
| 或（or）       | {$or : [{key01 : value01}, {key02 : value02}, ...]} | db.test.find( {$or:[{name : "《MongoDB 入门教程》"},{price : 24}]} ) | where name = "《MongoDB 入门教程》" or price = 24  |
| 非（nor）      | {$nor:[{key01: value01}, {key02: value02},...]      | db.test.find({$nor: [{price: 24}]})                          | where not price = 24                               |

[MongoDB逻辑操作符](https://blog.csdn.net/yaomingyang/article/details/75103480)

（2）查询null值

```
db.myCollection1.find({age:null})
```

此语句不仅匹配出 age 为 null 的文档，其他不同类型的文档也会被查出，相当于查询所有。这是因为 null 不仅会匹配某个键值为 null 的文档，而且还会匹配不包含这个键的文档。 

（3）查询数组

* 查询数组长度

查询键对应指定长度的数组

```
db.myCollection1.find({tags: {$size: 3}})
```

* 查询包含数组元素

```
db.myCollection1.find({tags: "mongodb"})
```

* 查询整个数组

```
db.myCollection1.find({tags:['MongoDB', 'NoSQL', 'database']})
```

（4）limit

 limit() 函数与 SQL 中的作用相同，用于限制查询结果的个数，如下语句只返回 3 个匹配的结果。若匹配的结果不到 3 个，则返回匹配数量的结果。

```
db.test.find().limit(3)
```

（5）skip

 Skip() 函数用于略过指定个数的文档，如下语句略过第一个文档，返回后两个。

```
db.test.find().skip(1)
```

（6）sort

 sort() 函数用于对查询结果进行排序，1 是升序，-1 是降序， 

```
db.test.find().sort({"price" : 1})
```

（7）$regex

使用 $regex 操作符来设置匹配字符串的正则表达式，不同于全文检索，使用正则表达式无须进行任何配置。 

```
// 使用正则表达式进行匹配，应该也可以进行模糊匹配
db.test.find({tags:{$regex:"MongoDB"}})
```

（8）游标

是指对数据一行一行地进行操作，在 MongoDB 数据库中对游标的控制非常简单，只需使用 firid() 函数就可以返回游标。

使用find函数进行查询时，MongoDB并不是一次返回结果集中的所有文档，而是分批次的返回结果，返回的数据量不会多余4M，默认情况下会返回结果集的101个文档或者1M的数据（看哪个条件先满足就用谁）。返回的文档会缓存到内存中，同时会有一个cursor游标指向该批结果集中的第一个，当游标遍历到完最后一个文档时，游标将自动向MongoDB数据库发送一个getMore数据请求得到下一批的结果集。 这样做有两个好处：

* 当查询结果很大时，批量返回结果的方式可以减少客户端等待的时间

* 批量返回结果集，减少游标重复连接客户端进行查询，减轻服务器负担。

有关游标的方法参见下表：

| 方法名          | 作用                                   |
| --------------- | -------------------------------------- |
| hasNext         | 判断是否有更多的文档                   |
| next            | 用来获取下一条文档                     |
| toArray         | 将查询结构放到数组中                   |
| count           | 查询的结果为文档的总数量               |
| limit           | 限制查询结果返回数量                   |
| skip            | 跳过指定数目的文档                     |
| sort            | 对查询结果进行排序                     |
| objsLeftlnBatch | 查看当前批次剩余的未被迭代的文档数量   |
| addOption       | 为游标设置辅助选项，修改游标的默认行为 |
| hint            | 为查询强制使用指定索引                 |
| explain         | 用于获取查询执行过程报告               |
| snapshot        | 对查询结果使用快照                     |

 使用游标时，需要注意下面 4 个问题：

* 当调用 find() 函数时，**Shell 并不立即查询数据库，而是等游标的hasNext使用时，才真正开始获取结果时才发送查询请求**。
* 游标对象的每个方法几乎都会返回游标对象本身，这样可以方便进行链式函数的调用。
* 在 MongoDB Shell 中使用游标输出文档包含两种情况，
  * 如果不将 find() 函数返回的游标赋值给一个局部变量进行保存，在默认情况下游标会自动迭代 20 次。
  * 如果将 find() 函数返回的游标赋值给一个局部变量，则可以使用游标对象提供的函数进行手动迭代。
* 使用清空后的游标，进行迭代输出时，显示的内容为空。

游标从创建到被销毁的整个过程存在的时间，被称为游标的生命周期，包括游标的创建、使用及销毁三个阶段。**当客户端使用 find() 函数向服务器端发起一次查询请求时，会在服务器端创建一个游标**，然后就可以使用游标函数来操作查询结果。 以下三种情况会让游标被销毁：

- 客户端保存的游标变量不在作用域内。
- 游标遍历完成后，或者客户端主动发送终止消息。
- 在服务器端 10 分钟内未对游标进行操作。

[ MongoDB数据查询 ](https://blog.51cto.com/12161108/1941241)

### 4. MongoDB索引

索引的作用是为了提升查询效率，在查询操作中，如果没有索引，MongoDB会扫描集合中的每个文档，以选择与查询语句匹配的文档。如果查询条件带有索引，MongoDB 将扫描索引， 通过索引确定要查询的部分文档，而非直接对全部文档进行扫描。 





























