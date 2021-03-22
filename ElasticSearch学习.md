# ElasticSearch学习

## 一、基本概念

### **1.ElasticSearch是什么**
ElasticSearch是一个基于Lucene的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。ElasticSearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。设计用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。构建在全文检索开源软件Lucene之上的Elasticsearch，不仅能对海量规模的数据完成分布式索引与检索，还能提供数据聚合分析。据国际权威的数据库产品评测机构DBEngines的统计，在2016年1月，Elasticsearch已超过Solr等，成为排名第一的搜索引擎类应用。

> 概括：
>
> 基于Restful标准的高扩展高可用的实时数据分析的全文搜索工具。

### **2.RESTful是什么**

RESTful是一种软件架构风格、设计风格，而不是标准，只是提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。在目前主流的三种Web服务交互方案中，REST相比于SOAP（Simple Object Access protocol，简单对象访问协议）以及XML-RPC更加简单明了。前后端分离主要是以API为界限进行解耦的，这就会产生大量的API，采用RESTful来设计API主要有以下好处：

* 表现力更强，更易于理解

* RESRful是无状态，所以不管前端是何种设备何种状态都可以无差别的请求资源

简单的说，RESTful是一种架构的规范与约束、原则，符合这种规范的架构就是RESTful架构。RESTful的核心就是后端将**资源**发布为**URI**，前端通过URI访问资源，并通过HTTP动词表示要对资源进行的操作。这里涉及到一个新概念：资源，后端提供的所有内容都可以被定义为资源。url为GET/POST/PUT/DELETE这种请求方式，且url中只有包含名词，类似于这种的url都是符合RESTful设计架构。

### **3.重要知识点**

* **接近实时（NRT）：**Elasticsearch是一个接近实时的搜索平台。这意味着，从索引一个文档直到这个文档能够被搜索到（增改操作）有一个**轻微的延迟（通常是1秒）**。

* **集群（cluster）：**一个集群就是由一个或多个节点组织在一起，它们共同持有你整个的数据，并一起提供索引和搜索功能。一个elasticsearch集群由一个唯一的名字标识，这个名字默认就是“elasticsearch”。这个名字是重要的，因为一个节点只能通过指定某个集群的名字，来加入这个集群。在产品环境中显式地设定这个名字是一个好习惯，但是使用默认值来进行测试/开发也是不错的。

* **节点（node）：**一个节点是你集群中的一个服务器，作为集群的一部分，它存储你的数据，参与集群的索引和搜索功能。和集群类似，一个节点也是由一个名字来标识的，默认情况下，这个名字是一个随机的漫威漫画角色的名字，这个名字会在启动的时候赋予节点。这个名字对于管理工作来说挺重要的，因为在这个管理过程中，你会去确定网络中的哪些服务器对应于Elasticsearch集群中的哪些节点。一个节点可以通过配置集群名称的方式来加入一个指定的集群。默认情况下，每个节点都会被安排加入到一个叫做“elasticsearch”的集群中，这意味着，如果你在你的网络中启动了若干个节点，并假定它们能够相互发现彼此，它们将会自动地形成并加入到一个叫做“elasticsearch”的集群中。在一个集群里，只要你想，可以拥有任意多个节点。而且，如果当前你的网络中没有运行任何Elasticsearch节点，这时启动一个节点，会默认创建并加入一个叫做“elasticsearch”的集群。

* **索引（Index）：**一个索引就是一个拥有几分相似特征的文档的集合。比如说，你可以有一个客户数据的索引，另一个产品目录的索引，还有一个订单数据的索引。一个索引由一个名字来标识（必须全部是小写字母的），并且当我们要对对应于这个索引中的文档进行索引、搜索、更新和删除的时候，都要使用到这个名字。索引类似于关系型数据库中Database的概念（只是拿来类比，不可以简单的认为索引就是Database）。在一个集群中，如果你想，可以定义任意多的索引。

* **类型（Type）：**在一个索引中，你可以定义一种或多种类型。一个类型是你的索引的一个逻辑上的分类/分区，其语义完全由你来定。通常，会为具有一组共同字段的文档定义一个类型。比如说，我们假设你运营一个博客平台并且将你所有的数据存储到一个索引中。在这个索引中，你可以为用户数据定义一个类型，为博客数据定义另一个类型，当然，也可以为评论数据定义另一个类型。类型类似于关系型数据库中Table的概念。

* **文档（Document）：**由于es存储的数据是文档型的，一条数据对应一篇文档即相当于mysql数据库中的一行数据row，一个文档中可以有多个字段也就是mysql数据库一行可以有多列。一个文档是一个可被索引的基础信息单元。比如，你可以拥有某一个客户的文档，某一个产品的一个文档，当然，也可以拥有某个订单的一个文档。文档以JSON（Javascript Object Notation）格式来表示，而JSON是一个到处存在的互联网数据交互格式。在一个index/type里面，只要你想，你可以存储任意多的文档。注意，**尽管一个文档，物理上存在于一个索引之中，文档必须被索引或者赋予一个索引的type。**文档类似于关系型数据库中Record的概念。实际上一个文档除了用户定义的数据外，还包括`_index、_type和_id`字段。

* **域（Field）：**es中一个文档中对应的多个列与mysql数据库中每一列对应。

* **映射（Mapping）：**Mapping是ES中的一个很重要的内容，它类似于传统关系型数据中table的schema，用于定义一个索引（index）的某个类型（type）的数据的结构。在ES中，我们无需手动创建type（相当于table）和mapping(相关与schema)。在默认配置下，ES可以根据插入的数据自动地创建type及其mapping。mapping中主要包括字段名、字段数据类型和字段索引类型。只不过有些时候es中的mapping增加了动态识别功能，感觉很强大的样子，其实实际生产环境上不建议使用，最好还是开始制定好了对应的schema为主。

* **分片和复制（shards & replicas）：**一个索引可能存储超出单个结点硬件限制的大量数据。比如，一个具有10亿文档的索引占据1TB的磁盘空间，而任一节点都没有这样大的磁盘空间；或者单个节点处理搜索请求，响应太慢。为了解决这个问题，Elasticsearch提供了将索引划分成多份的能力，这些份就叫做分片。当你创建一个索引的时候，你可以指定你想要的分片的数量。每个分片本身也是一个功能完善并且独立的“索引”，这个“索引”可以被放置到集群中的任何节点上。分片之所以重要，主要有两方面的原因：
  
  * 允许你水平分割/扩展你的内容容量
  
  * 允许你在分片（潜在地，位于多个节点上）之上进行分布式的、并行的操作，进而提高性能/吞吐量，至于一个分片怎样分布，它的文档怎样聚合回搜索请求，是完全由Elasticsearch管理的，对于作为用户的你来说，这些都是透明的。
  
  在一个网络/云的环境里，失败随时都可能发生，在某个分片/节点不知怎么的就处于离线状态，或者由于任何原因消失了。这种情况下，有一个故障转移机制是非常有用并且是强烈推荐的。为此目的，Elasticsearch允许你创建分片的一份或多份拷贝，这些拷贝叫做复制分片，或者直接叫复制。复制之所以重要，主要有两方面的原因：
  
    * 在分片/节点失败的情况下，提供了高可用性。因为这个原因，注意到复制分片从不与原/主要（original/primary）分片置于同一节点上是非常重要的。
  
    * 扩展你的搜索量/吞吐量，因为搜索可以在所有的复制上并行运行。
  
  总之，每个索引可以被分成多个分片。一个索引也可以被复制0次（意思是没有复制）或多次。一旦复制了，每个索引就有了主分片（作为复制源的原来的分片）和复制分片（主分片的拷贝）之别。分片和复制的数量可以在索引创建的时候指定。在索引创建之后，你可以在任何时候动态地改变复制数量，但是不能改变分片的数量。
  
  默认情况下，Elasticsearch中的每个索引被分片5个主分片和1个复制，这意味着，如果你的集群中至少有两个节点，你的索引将会有5个主分片和另外5个复制分片（1个完全拷贝），这样的话每个索引总共就有10个分片。一个索引的多个分片可以存放在集群中的一台主机上，也可以存放在多台主机上，这取决于你的集群机器数量。主分片和复制分片的具体位置是由ES内在的策略所决定的。
  
* **indexed：**就是名义上的建立索引。mysql中一般会对经常使用的列增加相应的索引用于提高查询速度，而在es中默认都是会加上索引的，除非你特殊制定不建立索引只是进行存储用于展示，这个需要看你具体的需求和业务进行设定了。

[ElasticSearch的基本原理与用法](https://www.cnblogs.com/luxiaoxun/p/4869509.html)

### **4.Elasticsearch的架构**

![image](https://images2015.cnblogs.com/blog/980882/201702/980882-20170208171207963-1711795457.png)

* **Gateway层：**es用来存储索引文件的一个文件系统且它支持很多类型，例如：本地磁盘、共享存储（做snapshot的时候需要用到）、hadoop的hdfs分布式存储、亚马逊的S3。它的主要职责是用来对数据进行长持久化以及整个集群重启之后可以通过gateway重新恢复数据。

* **Distributed Lucene Directory：**Gateway上层就是一个lucene的分布式框架，lucene是做检索的，但是它是一个单机的搜索引擎，像这种es分布式搜索引擎系统，虽然底层用lucene，但是需要在每个节点上都运行lucene进行相应的索引、查询以及更新，所以需要做成一个分布式的运行框架来满足业务的需要。

* **四大模块组件：**districted lucene directory之上就是一些es的模块，Index Module是索引模块，就是对数据建立索引也就是通常所说的建立一些倒排索引等；Search Module是搜索模块，就是对数据进行查询搜索；Mapping模块是数据映射与解析模块，就是你的数据的每个字段可以根据你建立的表结构通过mapping进行映射解析，如果你没有建立表结构，es就会根据你的数据类型推测你的数据结构之后自己生成一个mapping，然后都是根据这个mapping进行解析你的数据；River模块在es2.0之后应该是被取消了，它的意思表示是第三方插件，例如可以通过一些自定义的脚本将传统的数据库（mysql）等数据源通过格式化转换后直接同步到es集群里，这个river大部分是自己写的，写出来的东西质量参差不齐，将这些东西集成到es中会引发很多内部bug，严重影响了es的正常应用，所以在es2.0之后考虑将其去掉。

* **Discovery、Script：**es四大模块组件之上有 Discovery模块：es是一个集群包含很多节点，很多节点需要互相发现对方，然后组成一个集群（包括选主集群），这些es都是用的discovery模块，默认使用的是 Zen，也可是使用EC2；es查询还可以支撑多种script即脚本语言，包括mvel、js、python等等。

* **Transport协议层：**再上一层就是es的通讯接口Transport，支持的也比较多：Thrift、Memcached以及Http，默认的是http，JMX就是java的一个远程监控管理框架，因为es是通过java实现的。

* **RESTful接口层：**最上层就是es暴露给我们的访问接口，官方推荐的方案就是这种Restful接口，直接发送http请求，方便后续使用nginx做代理、分发包括可能后续会做权限的管理，通过http很容易做这方面的管理。如果使用java客户端它是直接调用api，在做负载均衡以及权限管理还是不太好做。

## 二、ElasticSearch基本操作

### 2.1倒排索引

Elasticsearch 使用一种称为 倒排索引 的结构，它适用于快速的全文搜索。一个倒排索引由文档中所有不重复词的列表构成，对于其中每个词，有一个包含它的文档列表。

示例：

(1)：假设文档集合包含五个文档，每个文档内容如图所示，在图中最左端一栏是每个文档对应的文档编号。我们的任务就是对这个文档集合建立倒排索引。

(2):中文和英文等语言不同，单词之间没有明确分隔符号，所以首先要用分词系统将文档自动切分成单词序列。这样每个文档就转换为由单词序列构成的数据流，为了系统后续处理方便，需要对每个不同的单词赋予唯一的单词编号，同时记录下哪些文档包含这个单词，在如此处理结束后，我们可以得到最简单的倒排索引。“单词ID”一栏记录了每个单词的单词编号，第二栏是对应的单词，第三栏即每个单词对应的倒排列表

(3):索引系统还可以记录除此之外的更多信息,下图还记载了单词频率信息（TF）即这个单词在某个文档中的出现次数，之所以要记录这个信息，是因为词频信息在搜索结果排序时，计算查询和文档相似度是很重要的一个计算因子，所以将其记录在倒排列表中，以方便后续排序时进行分值计算。

(4):倒排列表中还可以记录单词在某个文档出现的位置信息

(1,<11>,1),(2,<7>,1),(3,<3,9>,2)

有了这个索引系统，搜索引擎可以很方便地响应用户的查询，比如用户输入查询词“Facebook”，搜索系统查找倒排索引，从中可以读出包含这个单词的文档，这些文档就是提供给用户的搜索结果，而利用单词频率信息、文档频率信息即可以对这些候选搜索结果进行排序，计算文档和查询的相似性，按照相似性得分由高到低排序输出，此即为搜索系统的部分内部流程。

#### 2.1 倒排索引原理

1.The quick brown fox jumped over the lazy dog

2.Quick brown foxes leap over lazy dogs in summer

```
// 倒排索引后的结构

## Term      Doc_1   Doc_2

Quick   	|       |  X
The     	|   X   |
brown   	|   X   |  X
dog     	|   X   |
dogs    	|       |  X
fox     	|   X   |
foxes   	|       |  X
in      	|       |  X
jumped  	|   X   |
lazy    	|   X   |  X
leap    	|       |  X
over    	|   X   |  X
quick   	|   X   |
summer  	|       |  X
the     	|   X   |
```

搜索quick brown ：

```
## Term      Doc_1  Doc_2

brown   	|   X   |  X

quick   	|   X   |

Total       |   2   |  1
```

计算相关度分数时，文档1的匹配度高，分数会比文档2高

问题：

Quick 和 quick 以独立的词条出现，然而用户可能认为它们是相同的词。

fox 和 foxes 非常相似, 就像 dog 和 dogs ；他们有相同的词根。

jumped 和 leap, 尽管没有相同的词根，但他们的意思很相近。他们是同义词。

搜索含有 Quick fox的文档是搜索不到的

使用标准化规则(normalization)：

建立倒排索引的时候，会对拆分出的各个单词进行相应的处理，以提升后面搜索的时候能够搜索到相关联的文档的概率

```
Term      Doc_1  Doc_2

brown   |   X   |  X
dog     |   X   |  X
fox     |   X   |  X
in      |       |  X
jump    |   X   |  X
lazy    |   X   |  X
over    |   X   |  X
quick   |   X   |  X
summer  |       |  X
the     |   X   |  X
```

#### **2.1 分词器介绍及内置分词器**

分词器是从一串文本中切分出一个一个的词条，并对每个词条进行标准化，包括三部分：

* character filter：分词之前的预处理，过滤掉HTML标签，特殊符号转换等

* tokenizer：分词

* token filter：标准化

**内置分词器：**

* standard 分词器：(默认的)他会将词汇单元转换成小写形式，并去除停用词和标点符号，支持中文采用的方法为单字切分

* simple 分词器：首先会通过非字母字符来分割文本信息，然后将词汇单元统一为小写形式。该分析器会去掉数字类型的字符。

* Whitespace 分词器：仅仅是去除空格，对字符没有lowcase化，不支持中文；并且不对生成的词汇单元进行其他的标准化处理。

* language 分词器：特定语言的分词器，不支持中文。

### **2. 版本控制**

ElasticSearch采用了乐观锁来保证数据的一致性，也就是说，当用户对document进行操作时，并不需要对该document作加锁和解锁的操作，只需要指定要操作的版本即可。

当版本号一致时，ElasticSearch会允许该操作顺利执行，而当版本号存在冲突时，ElasticSearch会提示冲突并抛出异常（VersionConflictEngineException异常）。ElasticSearch的版本号的取值范围为1到2^63-1。

* 内部版本控制：使用的是_version

```
// 如果当前文档的版本是2，而传入的值是3，则更新的时候会抛异常
POST /lib/user/4?version = 3
```

* 外部版本控制：elasticsearch在处理外部版本号时会与对内部版本号的处理有些不同。它不再是检查`_version`是否与请求中指定的数值相同而是检查当前文档的`_version`是否比请求中指定的数值小。如果小则请求成功，那么外部的版本号就会被存储到文档中的_version中。为了保持_version与外部版本控制的数据一致，使用version_type = external。

```
// 如果当前文档的版本是2，而传入的值是4，则更新后当前文档的版本号hi变成4
POST /lib/user/4?version = 4&version_type = external
```

### **3.什么是Mapping**

mapping定义了type中的每个字段的数据类型以及这些字段如何分词等相关属性。

#### **3.1 索引的创建和获取**

创建索引的时候,可以预先定义字段的类型以及相关属性，这样就能够把日期字段处理成日期，把数字字段处理成数字，把字符串字段处理字符串值等。也可以不定义mapping，在第一次插入的数据的时候会自动分析创建数据对应的数据。

```
// 获取对应索引的mapping
GET /zpark3/_mapping?include_type_name=true

// 获取结果
{
  "myindex": {
    "mappings": {
      "article": {
        "properties": {
          "author_id": {
            "type": "long"
          },
          "content": {
            "type": "text",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          }
        }
      }
    }
  }
}
```

#### **3.2 mapping types 移除的原因**

`mapping types`这个概念在 ElasticSearch 7.X 已被完全移除，详细说明可以参考[官方文档](https://github.com/elastic/elasticsearch/blob/6.5/docs/reference/mapping/removal_of_types.asciidoc)

> 对于官方文档的字面解读：
>
> 处于一个 index的 不同 mapping types 中的同名字段内部还是基于相同的Lucene字段，也就是说一个 index 中的同名字段的 mapping 是相同的；而如果我们想避免拥有相同的字段，在存储不同的对象的时候，只有几个字段或者没有字段相同，则会导致数据稀疏，并干扰Lucene有效压缩文档的能力。以上两个都不是很好的创建mapping types 方法，所以直接移除这个概念。
>
> 个人理解：
>
> mapping types 是具有了 mapping 的 types，也就是每个类型中的字段都有映射。es 7.0 以后会逐渐移除 type 这个概念。

[ElasticSearch 6.x 学习笔记：映射Mapping](http://www.imooc.com/article/251430?block_id=tuijian_wz)

[映射类型（mapping type）从Elasticsearch中移除](https://blog.csdn.net/cnweike/article/details/79223542)

#### **3.3 支持的数据类型**

(1)核心数据类型（Core datatypes）

* 字符型：string，string类型包括 text 和 keyword。text类型被用来索引长文本，在建立索引前会将这些文本进行分词，转化为词的组合，建立索引。允许es来检索这些词语。text类型不能用来排序和聚合。Keyword类型不需要进行分词，可以被用来检索过滤、排序和聚合。keyword 类型字段只能用本身来进行检索。
* 数字型：long, integer, short, byte, double, float
* 日期型：date
* 布尔型：boolean
* 二进制型：binary   

(2)复杂数据类型（Complex datatypes）

* 数组类型（Array datatype）：数组类型不需要专门指定数组元素的type，例如：

```
字符型数组: [ "one", "two" ]
整型数组：[ 1, 2 ]
数组型数组：[ 1, [ 2, 3 ]] 等价于[ 1, 2, 3 ]
对象数组："persons" : [ { "name": "Mary", "age": 12 }, { "name": "John", "age": 10 }]
// 对象数组的底层存储为
"person.name" : ["Mary", "John"],
"person.age" : [12, 10]
```

* 对象类型（Object datatype）：object 用于单个JSON对象；

```
"person" : { "name": "Mary", "age": 12 }
// 底层存储格式为
"person.name" : ["Mary"],
"person.age" : [12]
```

* 嵌套类型（Nested datatype）： nested 用于JSON数组；

(3)地理位置类型（Geo datatypes）

* 地理坐标类型（Geo-point datatype）： geo_point 用于经纬度坐标；
* 地理形状类型（Geo-Shape datatype）： geo_shape 用于类似于多边形的复杂形状；

[ElasticSearch系列十一：掌握ES使用地理位置](https://blog.csdn.net/zhou870498/article/details/80529255)

(4)特定类型（Specialised datatypes）

* IPv4 类型（IPv4 datatype）：ip 用于IPv4 地址；
* Completion 类型（Completion datatype）：completion 提供自动补全建议；
* Token count 类型（Token count datatype）：token_count 用于统计做了标记的字段的index数目，该值会一直增加，不会因为过滤条件而减少。
* mapper-murmur3类型：通过插件，可以通过  murmur3  来计算 index 的 hash 值；
* 附加类型（Attachment datatype）：采用 mapper-attachments插件，可支持 attachments 索引，例如 Microsoft Office 格式，Open Document 格式，ePub, HTML 等。

（5）支持的属性

* "store":false//是否单独设置此字段的是否存储而从_source字段中分离，默认是false，只能搜索，不能获取值。

* "index": true//分词，不分词是：false，设置成false，字段将不会被索引。

* "analyzer":"ik"//指定分词器,默认分词器为standard analyzer。

* "boost":1.23//字段级别的分数加权，默认值是1.0。

* "doc_values":false//对not_analyzed字段，默认都是开启，分词字段不能使用，对排序和聚合能提升较大性能，节约内存。

* "fielddata":{"format":"disabled"}//针对分词字段，参与排序或聚合时能提高性能，不分词字段统一建议使用doc_value。

* "fields":{"raw":{"type":"string","index":"not_analyzed"}} //可以对一个字段提供多种索引模式，同一个字段的值，一个分词，一个不分词。
* "ignore_above":100 //超过100个字符的文本，将会被忽略，不被索引。

* "include_in_all":ture//设置是否此字段包含在_all字段中，默认是true，除非index设置成no选项。

* "index_options":"docs"//4个可选参数docs（索引文档号） ,freqs（文档号+词频），positions（文档号+词频+位置，通常用来距离查询），offsets（文档号+词频+位置+偏移量，通常被使用在高亮字段）分词字段默认是position，其他的默认是docs

* "norms":{"enable":true,"loading":"lazy"}//分词字段默认配置，不分词字段：默认{"enable":false}，存储长度因子和索引时boost，建议对需要参与评分字段使用 ，会额外增加内存消耗量

* "null_value":"NULL"//设置一些缺失字段的初始化值，只有string可以使用，分词字段的null值也会被分词

* "position_increament_gap":0//影响距离查询或近似查询，可以设置在多值字段的数据上或分词字段上，查询时可指定slop间隔，默认值是100

* "search_analyzer":"ik"//设置搜索时的分词器，默认跟ananlyzer是一致的，比如index时用standard+ngram，搜索时用standard用来完成自动提示功能

* "similarity":"BM25"//默认是TF/IDF算法，指定一个字段评分策略，仅仅对字符串型和分词类型有效

* "term_vector":"no"//默认不存储向量信息，支持参数yes（term存储），with_positions（term+位置）,with_offsets（term+偏移量），with_positions_offsets(term+位置+偏移量) 对快速高亮fast vector highlighter能提升性能，但开启又会加大索引体积，不适合大数据量用

### **4.使用ElasticSearch API 实现文档CRUD**

#### **4.1 集群操作**

```
// 1.查看集群中节点信息
GET /_cat/nodes?v

// 2.查看集群中的索引信息
GET /_cat/indices?v
// 对其中一些信息进行筛选查看
GET /_cat/indices?v&h=health,status,index
```

#### **4.2 索引操作**

**4.2.1 添加索引**

```
// 1. 使用自己的配置创建索引
PUT /lib/
{
    "settings":{
        "index":{
            "number_of_shards": 5,
            "number_of_replicas": 1
        }
    }
}

// 2.使用es默认配置
PUT lib2
```

**4.2.2 查看索引**

```
// 1.查看索引的配置
GET /lib/_settings

// 2.查看所有索引的配置
GET _all/_settings
```

**4.2.3 删除索引**

```
DELETE /baizhi
```

#### **4.3 类型操作**

**4.3.1 创建类型和mapping**

```
# 1.创建index(baizhi)并设置mapping，es 7.0以后推荐这种
PUT /baizhi
{
  "mappings": {
      "properties": { 
      # 注意： 字符串常用类型：text类型会分词 keyword类型不会分词
          "title":    { "type": "text"  },  
          "name":     { "type": "text"  }, 
          "age":      { "type": "integer" },  
          "created":  {
          "type":   "date", 
          "format": "strict_date_optional_time||epoch_millis"
          }
      }
  }
}

# 2.创建index(baizhi)并添加类型以及设置type(_doc)的mapping
PUT /baizhi?include_type_name=true
{
  "mappings": {
    "_doc": { 
      "properties": { 
      # 注意： 字符串常用类型：text类型会分词 keyword类型不会分词
        "title":    { "type": "text"  },  
        "name":     { "type": "text"  }, 
        "age":      { "type": "integer" },  
        "created":  {
          "type":   "date", 
          "format": "strict_date_optional_time||epoch_millis"
        }
      }
    }
  }
}

# 3.创建index(baizhi)后，在指定index(user)中添加类型mapping(user)
POST /baizhi/user
{
  "user": { 
      "properties": { 
        "id":    { "type": "text"  }, 
        "name":     { "type": "text"  }, 
        "age":      { "type": "integer" },  
        "created":  {
          "type":   "date", 
          "format": "strict_date_optional_time||epoch_millis"
        }
      }
    }
}
```

* **Mapping Type：**
  1. 简单类型： [`text`](https://www.elastic.co/guide/en/elasticsearch/reference/current/text.html), [`keyword`](https://www.elastic.co/guide/en/elasticsearch/reference/current/keyword.html), [`date`](https://www.elastic.co/guide/en/elasticsearch/reference/current/date.html), [`long`](https://www.elastic.co/guide/en/elasticsearch/reference/current/number.html), [`double`](https://www.elastic.co/guide/en/elasticsearch/reference/current/number.html), [`boolean`](https://www.elastic.co/guide/en/elasticsearch/reference/current/boolean.html) or [`ip`](https://www.elastic.co/guide/en/elasticsearch/reference/current/ip.html)
  2. 其它类型： [`object`](https://www.elastic.co/guide/en/elasticsearch/reference/current/object.html), [`geo_point`](https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-point.html), [`geo_shape`](https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-shape.html)等

**4.3.2 查看类型的mapping**

```
# 1.语法：GET /索引名/_mapping/类型名
GET /baizhi/_doc/_mapping?include_type_name=true

# 2.语法：GET /索引名/_mapping
GET /baizhi/_mapping?include_type_name=true
```

注意 ：每个版本的es都会有所不同，注意每个版本的差别。mapping types将会在ES 7.0版本中移除。[原因可参考](https://www.elastic.co/guide/en/elasticsearch/reference/current/removal-of-types.html#_why_are_mapping_types_being_removed)

#### **4.4 文档操作**

**4.4.1 新增文档**

```
// 指定id的值
# put /索引名/类型名/id
PUT /lib/user/1
{
    "first_name" :  "Jane",
    "last_name" :   "Smith",
    "age" :         32,
    "about" :       "I like to collect rock albums",
    "interests":  [ "music" ]
}

// 不指定id的值
# post /索引名/类型名/
POST /lib/user
{
    "first_name" :  "Douglas",
    "last_name" :   "Fir",
    "age" :         23,
    "about":        "I like to build cabinets",
    "interests":  [ "forestry"]
}
```

**4.4.2 查询单个文档**

```
# 语法： GET /索引名/类型名/id
GET /baizhi/_doc/1	
```

**4.4.3 修改单个文档**

在 Elasticsearch 中 文档是不可改变 的，不能修改它们。 相反，如果想要更新现有的文档，需要重建索引或者进行替换。在内部，Elasticsearch已将旧文档标记为已删除，并增加一个全新的文档。 尽管你不能再对旧版本的文档进行访问，但它并不会立即消失。当继续索引更多的数据，Elasticsearch会在后台清理这些已删除文档。

（1）完全重新索引，更改此id到新的JSON数据上

```
# 语法： PUT /索引名/类型名/id
PUT /zpark/user/2 
{
  "name":"lxs",
  "title":"李小四",
  counter : 1,
  "tags": [
  	"red"
  ]
}
```

（2）脚本更新文档

```
// 1.对指定的字段进行计算操作
POST /zpark/user/2/_update
{
	"script" : "ctx._source.conter += params.count",
	"lang" : "painless" # 使用painless脚本语言
	"params" : {
		"count" : 4
	}
	# 如果文档尚不存在，则会将upsert元素的内容作为新文档插入。 如果文档确实存在，那么脚本将被执行
	 "upsert" : {
        "counter" : 1
    }
    "scripted_upsert" : true,
}

// 2.对指定的数组添加元素
POST /zpark/user/2/_update
{
    "script" : {
        "source": "ctx._source.tags.add(params.tag)",
        "lang": "painless",
        "params" : {
            "tag" : "blue"
        }
    }
}

// 3.添加新的字段
POST /zpark/user/2/_update
{
    "script" : "ctx._source.new_field = 'value_of_new_field'"
}

// 4.删除某个字段
POST /zpark/user/2/_update
{
    "script" : "ctx._source.remove('new_field')"
}

// 5.对某个字段值进行循环/判断操作
POST /zpark/user/2/_update
{
    "script" : {
    # 删除包含 blue 的tag字段
        "source": "if (ctx._source.tags.contains(params.tag)) { ctx.op = 'delete' } else { ctx.op = 'none' }",
        "lang": "painless",
        "params" : {
            "tag" : "blue"
        }
    }
}
```

除`_source`外，通过`ctx`映射还可以使用以下变量：`_index,_type,_id,_version,_routing,_parent和_now（当前时间戳）。`

（3）传递部分文档

```
POST /lib/user/1/_update
{
  "doc":{
  "name" : "lxs修"
  }
  # 关闭文档没有任何更新的检查，意思是每次都会更新文档
  "detect_noop" : false
  # 将不会发送部分文档和upsert文档，而是将doc的内容用作upsert值
  "doc_as_upsert" : true
}
```

**注意：**

* 如果指定了文档和脚本，则doc将被忽略，即脚本内容可覆盖doc， 最好的办法是将你的部分文档的字段对放入脚本本身。如果指定doc，则其值与现有的_source合并。
* 默认情况下，不更改任何内容的更新会检测到它们不会更改任何内容，并返回 “result”：“noop”，需要加"detect_noop"属性关闭。 

[ES学习之更新API](https://blog.csdn.net/weixin_39471249/article/details/80427339)

**4.4.4 删除单个文档**

```
DELETE /baizhi/_doc/1
```

**4.4.5 批量获取文档**

（1）获取指定id的文档

```
GET /_mget
{
"docs":[
   {
       "_index": "lib",
       "_type": "user",
       "_id": 1
   },
   {
       "_index": "lib",
       "_type": "user",
       "_id": 2
   },
   {
       "_index": "lib",
       "_type": "user",
       "_id": 3
   }
 ]
}

// 也可以简写成
GET /lib/user/_mget
{
   "ids": ["1","2"]
}
```

（2）获取指定具体的字段的文档

```
GET /_mget
{
"docs":[
   {
       "_index": "lib",
       "_type": "user",
       "_id": 1,
       "_source": "interests"
   },
   {
       "_index": "lib",
       "_type": "user",
       "_id": 2,
       "_source": ["age","interests"]
   }
 ]
}
```

**4.4.6 批处理操作**

（1）批处理操作的格式

```
POST 索引/类型/_bulk
{action : {"_id" : "id值"}}
#!记住这里的数据要写在一行 不能回车
{requestbody}

// 第二种方式
POST /_bulk
{action : {"_index" : "索引", "_type" : "类型", "_id" : "id
值"}}
{requestbody}
```

有四种action

* create：文档不存在时创建
* update:更新文档
* index:创建新文档或替换已有文档
* delete:删除一个文档

**注意：**如果数据存在，使用create操作失败，会提示文档已经存在，使用index则可以成功执行。

（2）批处理添加

```
POST /baizhi/_doc/_bulk 
# 指定id则使用指定的值，不指定则默认uuid
{"index":{_id : 1}}
{"name":"ww","title":"王五","age":18,"created":"2018-12-27"}
{"index":{}}
{"name":"zl","title":"赵六","age":25,"created":"2018-12-27"}
```

（3）批处理删除和修改

```
POST /baizhi/_doc/_bulk	 # 批量操作（包含修改和删除）
# 修改
{"update":{"_id":"KrOP6WcBVEuCC3JS8V9K"}} 
# 这里可以用脚本或者部分修改 
{"doc":{"title":"王小五"}}
# 删除
{"delete":{"_id":"K7OP6WcBVEuCC3JS8V9K"}}  
```

> bulk一次最大处理多少数据量:
>
> bulk会把将要处理的数据载入内存中，所以数据量是有限制的，最佳的数据量不是一个确定的数值，它取决于你的硬件，你的文档大小以及复杂性，你的索引以及搜索的负载。
>
> 一般建议是1000-5000个文档，大小建议是5-15MB，默认不能超过100M，可以在es的配置文件（即$ES_HOME下的config下的elasticsearch.yml）中修改。

## 三、深入搜索

搜索有两种方式：一种是通过`URL`参数进行搜索，另一种是通过`DSL(Request Body)`进行搜索。

### **1.基本查询(Query查询)**

#### **1.1 数据准备**

```
PUT /lib3
{
    "settings":{
        "number_of_shards" : 3,
        "number_of_replicas" : 0
    },
    "mappings":{
        "user":{
            "properties":{
                "name": {"type":"text"},
                "address": {"type":"text"},
                "age": {"type":"integer"},
                "interests": {"type":"text"},
                "birthday": {"type":"date"}
        	}
        }
    }
}
```

GET /lib3/user/_search?q=name:lisi

GET /lib3/user/_search?q=name:zhaoliu&sort=age:desc

#### **1.2 term查询和terms查询**

term query会去倒排索引中寻找确切的term，它并不知道分词器的存在。这种查询适合keyword 、numeric、date。

（1）term：查询某个字段里含有某个关键词的文档

```
GET /lib3/user/_search/
{
  "query": {
      "term": {"interests": "changge"}
  }
}
```

（2）terms：查询某个字段里含有多个关键词的文档，相当于数据库中的in查询

```
GET /lib3/user/_search
{
    "query":{
        "terms":{
            "interests": ["hejiu","changge"]
        }
    }
}
```

#### **1.3 控制查询返回的数量**

from：从哪一个文档开始；size：需要的个数

```
GET /lib3/user/_search
{
    "from":0,
    "size":2,
    "query":{
        "terms":{
            "interests": ["hejiu","changge"]
        }
    }
}
```

#### **1.4 返回版本号**

```
GET /lib3/user/_search
{
    "version":true,
    "query":{
        "terms":{
            "interests": ["hejiu","changge"]
        }
    }
}
```

#### **1.5 match查询**

（1）match知道分词器的存在，会对filed进行分词操作，然后再查询

```
GET /lib3/user/_search
{
    "query":{
        "match":{
            "name": "zhaoliu"
        }
    }
}
```

（2）match_all：查询所有文档

```
GET /lib3/user/_search
{
  "query": {
    "match_all": {}
  }
}
```

（3）multi_match：可以指定多个字段，查询多个字段的并集

```
GET /lib3/user/_search
{
    "query":{
        "multi_match": {
        	# 要搜索的值
            "query": "lvyou",
            # 字段
            "fields": ["interests","name"]
         }
    }
}
```

（4）match_phrase：短语匹配查询

ElasticSearch引擎首先分析（analyze）查询字符串，从分析后的文本中构建短语查询，这意味着必须匹配短语中的所有分词，并且保证各个分词的相对位置不变。

```
// 例：中关村大街新中关商城
GET lib3/user/_search
{
  "query":{  
      "match_phrase":{  
         "interests": 
         	"query" : "大街，中", # 要连贯的查询
         	"slop" : 2 #! 两个term的位置之间允许的最大间隔距离
      }
   }
}
```

#### **1.6 指定返回的字段**

```
GET /lib3/user/_search
{
    "_source": ["address","name"],
    "query": {
        "match": {
            "interests": "changge"
        }
    }
}
```

#### **1.7 控制加载的字段**

```
GET /lib3/user/_search
{
    "query": {
        "match_all": {}
    },
    "_source": {
          "includes": ["name","address"],
          "excludes": ["age","birthday"]
      }
}
```

（2）使用通配符*

```
GET /lib3/user/_search
{
    "_source": {
        "includes": "addr*",
        "excludes": ["name","bir*"]
    },
    "query": {
    	"match_all": {}
    }
}
```

#### **1.8 排序**

使用sort实现排序：desc（降序），asc（升序）。

```
GET /lib3/user/_search
{
    "query": {
        "match_all": {}
    },
    "sort": [
        {
           "age": {
               "order":"asc"
           }
        }
    ]
}
```

#### **1.9 前缀匹配查询**

```
GET /lib3/user/_search
{
  "query": {
    "match_phrase_prefix": {
        "name": {
            "query": "zhao"
        }
    }
  }
}

// 或

GET /lib3/user/_search
{
  "query": {
    "match_phrase_prefix": {
        "name": "zhao"
    }
  }
}
```

#### **1.10 范围查询**

range：实现范围查询，参数：from，to，include_lower（是否包含范围的左边界，默认是true），include_upper（是否包含范围的右边界，默认是true），boost

```
GET /lib3/user/_search
{
    "query": {
        "range": {
            "age": {
                "from": 20,
                "to": 25,
                "include_lower": true,
                "include_upper": false
            }
        }
    }
}
```

#### **1.11 wildcard查询**

通配符，允许使用通配符 * （代表0个或多个字符）和 ? （代表任意一个字符）来进行查询。

```
GET /lib3/user/_search
{
    "query": {
        "wildcard": {
             "name": "zhao*"
        }
    }
}
```

#### **1.12 fuzzy实现模糊查询**

搜索的时候，可能输入的搜索文本会出现误拼写的情况，fuzzy自动将拼写错误的搜索文本，进行纠正，纠正以后去尝试匹配索引中的数据，纠正是在一定的范围内如果差别大无法搜索出来

* value：查询的关键字
* boost：查询的权值，默认值是1.0，也就是影响查询的评分。
* min_similarity：设置匹配的最小相似度，默认值为0.5，对于字符串，取值为0-1(包括0和1)；对于数值，取值可能大于1；对于日期型取值为1d、1m等，1d就代表1天。
* max_expansions：查询中的词项可以扩展的数目，默认可以无限大。
* prefix_length：指明区分词项的共同前缀长度，默认是0
* fuzziness：纠正搜索参数的次数

```
GET /lib3/user/_search
{
    "query": {
        "fuzzy": {
             "interests": {
             	"value" : "chagge"
             }
        }
    }
}
```

[Lucene5学习之FuzzyQuery使用](https://www.iteye.com/blog/iamyida-2195066)

#### **1.13 高亮搜索结果**

```
GET /lib3/user/_search
{
    "query":{
        "match":{
            "interests": "changge"
        }
    },
    "highlight": {
        "fields": {
             "interests": {}
        }
    }
}
```

[ElasticSearch基本查询](https://blog.csdn.net/handong106324/article/details/50184095)

### **2. Filter查询**

filter是不计算相关性的，同时可以cache。因此，filter速度要快于query。

```
POST /lib4/items/_bulk
{"index": {"_id": 1}}
{"price": 40,"itemID": "ID100123"}
{"index": {"_id": 2}}
{"price": 50,"itemID": "ID100124"}
{"index": {"_id": 3}}
{"price": 25,"itemID": "ID100124"}
{"index": {"_id": 4}}
{"price": 30,"itemID": "ID100125"}
{"index": {"_id": 5}}
{"price": null,"itemID": "ID100127"}
```

#### **2.1 简单的过滤查询**

```
GET /lib4/items/_search
{
      "post_filter": {
          "terms": {
                 "price": [25,40]
              }
        }
}
```

查看分词器分析的结果：

```
GET /lib4/_mapping
```

不希望商品id字段被分词，则重新创建映射

```
DELETE lib4

PUT /lib4
{
    "mappings": {
        "items": {
            "properties": {
                "itemID": {
                    "type": "text",
                    "index": false
                }
            }
        }
    }
}
```

#### **2.2 bool过滤查询**

可以实现组合过滤查询

```
// 格式
{
    "bool": {
        "must": [],    # must:必须满足的条件---and
        "should": [],  # should：可以满足也可以不满足的条件--or
        "must_not": [] # must_not:不需要满足的条件--not
    }
}
```

  bool基本使用   

```
GET /lib4/items/_search
{
    "post_filter": {
        "bool": {
            "should": [
            	{"term": {"price":25}},
            	{"term": {"itemID": "id100123"}}
            ],
            "must_not": {
            	"term":{"price": 30}
            }
        }
    }
}
```

嵌套使用bool：

```
// 查询 itemID 为 id100123 和 id100124 的文档，其中 id100124 的价格必须为 40
GET /lib4/items/_search
{
    "post_filter": {
        "bool": {
                "should": [
                {"term": {"itemID": "id100123"}},
                {"bool": {
                    "must": [
                        {"term": {"itemID": "id100124"}},
                        {"term": {"price": 40}}
                    ]}
                }
            ]
        }
    }
}
```

#### **2.3 范围过滤**

gt（大于），（lt）小于，（gte）大于等于，（lte）小于等于

```
GET /lib4/items/_search
{
     "post_filter": {
          "range": {
              "price": {
                   "gt": 25,
                   "lt": 50
                }
            }
      }
}
```

#### **2.4 过滤非空**

```
GET /lib4/items/_search
{
  "query": {
    "bool": {
      "filter": {
          "exists":{
             "field":"price"
         }
      }
    }
  }
}

// 或

GET /lib4/items/_search
{
    "query" : {
        "constant_score" : {
            "filter": {
                "exists" : { 
                	"field" : "price" 
                }
            }
        }
    }
}
```

#### **2.5 过滤器缓存**

ElasticSearch提供了一种特殊的缓存，即过滤器缓存（filter cache），用来存储过滤器的结果，被缓存的过滤器并不需要消耗过多的内存（因为它们只存储了哪些文档能与过滤器相匹配的相关信息），而且可供后续所有与之相关的查询重复使用，从而极大地提高了查询性能。ElasticSearch并不是默认缓存所有过滤器。

以下过滤器默认不缓存：

```
numeric_range，script，geo_bbox
geo_distance，geo_distance_range，geo_polygon，geo_shape
and，or，not
```

以下过滤器默认开启缓存：

```
exists，missing，range，term，terms
```

开启方式：在filter查询语句后边加上：（目前不知道怎么用）

```
"_catch":true
```

[es的三种缓存](https://www.jianshu.com/p/e8fd5c31c020)

### **3. 聚合查询**

#### **3.1 sum总数**

```
GET /lib4/items/_search
{
// size不设置为0，除了返回聚合结果外，还会返回其它所有的数据
  "size":0,
  "aggs": {
     "price_of_sum": {
         "sum": {
           "field": "price"
         }
     }
  }
}
```

#### **3.2 min最小数**

```
GET /lib4/items/_search
{
  "size": 0, 
  "aggs": {
     "price_of_min": {
         "min": {
           "field": "price"
         }
     }
  }
}
```

#### **3.3 max最大值**

```
GET /lib4/items/_search
{
  "size": 0, 
  "aggs": {
     "price_of_max": {
         "max": {
           "field": "price"
         }
     }
  }
}
```

#### **3.4 avg平均值**

```
GET /lib4/items/_search
{
  "size":0,
  "aggs": {
     "price_of_avg": {
         "avg": {
           "field": "price"
         }
     }
  }
}
```

#### **3.5 cardinality基数**

也就是去重查询

```
GET /lib4/items/_search
{
  "size":0,
  "aggs": {
     "price_of_cardi": {
         "cardinality": {
           "field": "price"
         }
     }
  }
}
```

[ElastaticSearch 去重cardinality的坑](https://blog.csdn.net/zhongyuan_1990/article/details/52585994)

#### **3.6 terms分组**

相当于MySQL的group by操作，所以不要尝试对es中text的字段进行桶聚合，否则会失败。

```
GET /lib4/items/_search
{
  "size":0,
  "aggs": {
     "price_group_by": {
         "terms": {
           "size":100,
           "field": "price"，
           "min_doc_count":1
         }
     }
  }
}
```

#### **3.7 stats基本统计**

```
GET /lib4/items/_search
{
  "size":0,
  "aggs": {
     "stats_id": {
         "stats": {
           "field": "price"
         }
     }
  }
}
```

#### **3.8 练习**

```
// 对那些有唱歌兴趣的用户按年龄分组
GET /lib3/user/_search
{
  "query": {
      "match": {
        "interests": "changge"
      }
   },
   "size": 0, 
   "aggs":{
       "age_group_by":{
           "terms": {
             "field": "age",
             "order": {
               "avg_of_age": "desc"
             }
           },
           "aggs": {
             "avg_of_age": {
               "avg": {
                 "field": "age"
               }
             }
           }
       }
   }
}
```

### **4. 复合查询**

将多个基本查询组合成单一查询的查询

#### **4.1 使用bool查询**

接收以下参数：

must：
    文档 必须匹配这些条件才能被包含进来。 
    
must_not：
    文档 必须不匹配这些条件才能被包含进来。 
    
should：
    如果满足这些语句中的任意语句，将增加 _score，否则，无任何影响。**它们主要用于修正每个文档的相关性得分**。 
    
filter：
    必须 匹配，但它以不评分、过滤模式来进行。这些语句对评分没有贡献，只是根据过滤标准来排除或包含文档。
    
相关性得分是如何组合的。每一个子查询都独自地计算文档的相关性得分。一旦他们的得分被计算出来， bool 查询就将这些得分进行合并并且返回一个代表整个布尔操作的得分。

下面的查询用于查找 title 字段匹配 how to make millions 并且不被标识为 spam 的文档。那些被标识为 starred 或在2014之后的文档，将比另外那些文档拥有更高的排名。如果两者都满足，那么它排名将更高：

```
{
    "bool": {
        "must": { "match": { "title": "how to make millions" }},
        "must_not": { "match": { "tag":   "spam" }},
        "should": [
            { "match": { "tag": "starred" }},
            { "range": { "date": { "gte": "2014-01-01" }}}
        ]
    }
}
```

如果没有 must 语句，那么至少需要能够匹配其中的一条 should 语句。但如果存在至少一条 must 语句，则对 should 语句的匹配没有要求。如果我们不想因为文档的时间而影响得分，可以用 filter 语句来重写前面的例子：

```
{
    "bool": {
        "must": { "match": { "title": "how to make millions" }},
        "must_not": { "match": { "tag":   "spam" }},
        "should": [
            { "match": { "tag": "starred" }}
        ,
        "filter": {
          "range": { "date": { "gte": "2014-01-01" }} 
        }],
    }
}
```

通过将 range 查询移到 filter 语句中，我们将它转成不评分的查询，将不再影响文档的相关性排名。由于它现在是一个不评分的查询，可以使用各种对 filter 查询有效的优化手段来提升性能。

bool 查询本身也可以被用做不评分的查询。简单地将它放置到 filter 语句中并在内部构建布尔逻辑：

```
{
    "bool": {
        "must": { "match": { "title": "how to make millions" }},
        "must_not": { "match": { "tag":   "spam" }},
        "should": [
            { "match": { "tag": "starred" }}
        ],
        "filter": {
          "bool": { 
              "must": [
                  { "range": { "date": { "gte": "2014-01-01" }}},
                  { "range": { "price": { "lte": 29.99 }}}
              ],
              "must_not": [
                  { "term": { "category": "ebooks" }}
              ]
          }
        }
    }
}
```

#### **4.2 constant_score查询**

它将一个不变的常量评分应用于所有匹配的文档。它被经常用于你只需要执行一个 filter 而没有其它查询（例如，评分查询）的情况下。

{
    "constant_score":   {
        "filter": {
            "term": { "category": "ebooks" } 
        }
    }
}

term 查询被放置在 constant_score 中，转成不评分的filter。这种方式可以用来取代只有 filter 语句的 bool 查询。 

## 四、ElasticSearch原理

### **1. 解析es的分布式架构**

#### **1.1 分布式架构的透明隐藏特性**

ElasticSearch是一个分布式系统，隐藏了复杂的处理机制

* 分片机制：我们不用关心数据是按照什么机制分片的、最后放入到哪个分片中

* 集群发现机制(cluster discovery)：比如当前我们启动了一个es进程，当启动了第二个es进程时，这个进程作为一个node自动就发现了集群，并且加入了进去

* shard负载均衡：比如现在有10shard，集群中有3个节点，es会进行均衡的进行分配，以保持每个节点均衡的负载请求

#### **1.2 扩容机制**

* 垂直扩容：购置新的机器，替换已有的机器

* 水平扩容：直接增加机器

#### **1.3 自动均衡**

增加或减少节点时会自动均衡

#### **1.4 master节点**

主节点的主要职责是和集群操作相关的内容，如创建或删除索引，跟踪哪些节点是群集的一部分，并决定哪些分片分配给相关的节点。稳定的主节点对集群的健康是非常重要的。

#### **1.5 节点对等**

每个节点都能接收请求；每个节点接收到请求后都能把该请求路由到有相关数据的其它节点上；接收原始请求的节点负责采集数据并返回给客户端。

### **2. 分片和副本机制**

* index包含多个shard

* 每个shard都是一个最小工作单元，承载部分数据；每个shard都是一个lucene实例，有完整的建立索引和处理请求的能力

* 增减节点时，shard会自动在nodes中负载均衡

* primary shard和replica shard，每个document肯定只存在于某一个primary shard以及其对应的replica shard中，不可能存在于多个primary shard

* replica shard是primary shard的副本，负责容错，以及承担读请求负载

* primary shard的数量在创建索引的时候就固定了，replica shard的数量可以随时修改

* primary shard的默认数量是5，replica默认是1（也就是把每个主分片复制一份），默认有10个shard，5个primary shard，5个replica shard

* primary shard不能和自己的replica shard放在同一个节点上（否则节点宕机，primary shard和副本都丢失，起不到容错的作用），但是可以和其他primary shard的replica shard放在同一个节点上

### **3. 单节点环境下创建索引分析**

```
PUT /myindex
{
   "settings" : {
      "number_of_shards" : 3,
      "number_of_replicas" : 1
   }
}
```

这个时候，只会将3个primary shard分配到仅有的一个node上去，另外3个replica shard是无法分配的（一个shard的副本replica，他们两个是不能在同一个节点的）。集群可以正常工作，但是一旦出现节点宕机，数据全部丢失，而且集群不可用，无法接收任何请求。

### **4. 两个节点环境下创建索引分析**

* 将3个primary shard分配到一个node上去，另外3个replica shard分配到另一个节点上

* primary shard 和 replica shard 保持同步

* primary shard 和 replica shard 都可以处理客户端的读请求

### **5. 水平扩容的过程**

* 扩容后primary shard和replica shard会自动的负载均衡

* 扩容后每个节点上的shard会减少，那么分配给每个shard的CPU、内存、IO资源会更多，性能提高

* 扩容的极限，如果有6个shard，扩容的极限就是6个节点，每个节点上一个shard，如果想超出扩容的极限，比如说扩容到9个节点，那么可以增加replica shard的个数

* 6个shard，3个节点，最多能承受几个节点所在的服务器宕机？(容错性)
  任何一台服务器宕机都会丢失部分数据，为了提高容错性，增加shard的个数，9个shard，(3个primary shard，6个replicashard)，这样就能容忍最多两台服务器宕机了

**总结：**扩容是为了提高系统的吞吐量，同时也要考虑容错性，也就是让尽可能多的服务器宕机还能保证数据不丢失

### **6. ElasticSearch的容错机制**

以9个shard，3个节点为例。如果master node 宕机，此时不是所有的primary shard都是Active status，所以此时的集群状态是red。

* 容错处理的第一步：是选举一台服务器作为master

* 容错处理的第二步：新选举出的master会把挂掉的primary shard的某个replica shard 提升为primary shard，此时集群的状态为yellow，因为少了一个replica shard，并不是所有的replica shard都是active status

* 容错处理的第三步：重启故障机，新master会把所有的primary shard都复制一份副本到该节点上，（同步一下宕机后发生的修改），此时集群的状态为green，因为所有的primary shard和replica shard都是Active status

### **7. 文档的核心元数据**

(1)_index

说明了一个文档存储在哪个索引中，同一个索引下存放的是相似的文档(文档的field多数是相同的)，索引名必须是小写的，不能以下划线开头，不能包括逗号。

(2)_type:

表示文档属于索引中的哪个类型，一个索引下只能有一个type，类型名可以是大写也可以是小写的，不能以下划线开头，不能包括逗号。

(3)_id:

文档的唯一标识，和索引，类型组合在一起唯一标识了一个文档，可以手动指定值，也可以由es来生成这个值。

### **8. 文档id生成方式**

(1)手动指定

  put /index/type/66

  通常是把其它系统的已有数据导入到es时

(2)由es生成id值

  post /index/type

 es生成的id长度为20个字符，使用的是base64编码，URL安全，使用的是GUID算法，分布式下并发生成id值时不会冲突

### **9. _source元数据分析**

其实就是我们在添加文档时request body中的内容

指定返回的结果中含有哪些字段：

get /index/type/1?_source=name

### **10. partial update 处理并发冲突**

使用的是乐观锁:_version

retry_on_conflict：重新获取文档数据和版本信息进行更新，不断的操作，最多操作的次数就是retry_on_conflict的值

POST /lib/user/4/_update?retry_on_conflict=3

### **11. 文档数据路由原理解析**

#### **11.1 文档路由到分片上**

 一个索引由多个分片构成，当添加(删除，修改)一个文档时，es就需要决定这个文档存储在哪个分片上，这个过程就称为数据路由(routing)

#### **11.2 路由算法：**

```
 shard=hash(routing) % number_of_pirmary_shards
```

示例：一个索引，3个primary shard

* 每次增删改查时，都有一个routing值，默认是文档的_id的值 

* 对这个routing值使用哈希函数进行计算

* 计算出的值再和主分片个数取余数

**注意：**

* 余数肯定在0---（number_of_pirmary_shards-1）之间，文档就在对应的shard上

* routing值默认是文档的_id的值，也可以手动指定一个值，手动指定对于负载均衡以及提高批量读取的性能都有帮助

* primary shard个数一旦确定就不能修改了 

### **12. 文档增删改查内部原理**

大概的流程如下：

* 发送增删改请求时，可以选择任意一个节点，该节点就成了协调节点(coordinating node)
* 协调节点使用路由算法进行路由，然后将请求转到primary shard所在节点，该节点处理请求，并把数据同步到它的replica shard 
* 协调节点对客户端做出响应 

#### **12.1 文档增加的原理**

![文档增加原理](.\Elasticsearch\文档增加原理.png)

* 发送增加文档请求时，任一节点接收，此节点称为协调节点，根据路由选择，找到主分片所在的节点。
* 当分片所在的节点接收到来自协调节点的请求后，会将该请求写入translog，并将文档加入内存缓存。如果请求在主分片上成功处理，该请求会并行发送到该分片的副本上。当translog被同步到全部的主分片及其副本上后，客户端才会收到确认通知。
* 在主分片内部，内存缓冲会被周期性刷新(默认是1秒)或者在存储一定量数据的时候刷新，内容将被写到文件系统缓存的一个新段（segment）上。虽然这个段并没有被同步(fsync)，但它是开放的，内容可以被搜索到但不可以修改。
* 每30分钟，或者当translog很大的时候，translog会被清空，文件系统缓存会被同步。这个过程在Elasticsearch中称为冲洗(flush)。在冲洗过程中，translog被清除，内容被写入一个新段并存入文件系统缓存中。新段同步时将创建一个新的提交点，并将内容刷新到磁盘。旧的translog将被删除并开始一个新的translog。

> **当segment从文件系统缓存同步到磁盘时发生了错误怎么办？ 数据会不会丢失**？
>
> 由于Elasticsearch 在把数据写入到内存缓冲的同时，其实还另外记录了一个 translog日志，如果在这期间故障发生时，Elasticsearch会从commit位置开始，恢复整个translog文件中的记录，保证数据的一致性。
>
> 等到真正把 segment 刷新到磁盘，且 commit 文件进行更新的时候， translog 文件才清空。这一步，叫做flush。同样，Elasticsearch 也提供了 /_flush 接口。

#### **12.2 更新删除文档的原理**

删除和更新也都是写操作。但是Elasticsearch中的文档是不可变的，因此不能被删除或者改动以展示其变更。

* 执行删除操作：磁盘上的每个段都有一个相应的.del文件。当删除请求发送后，文档并没有真的被删除，而是在.del文件中被标记为删除。该文档依然能匹配查询，但是会在结果中被过滤掉。当段合并时，在.del文件中被标记为删除的文档将不会被写入新段。

* 执行更新操作：在新的文档被创建时，Elasticsearch会为该文档指定一个版本号。当执行更新时，旧版本的文档在.del文件中被标记为删除，新版本的文档被索引到一个新段。旧版本的文档依然能匹配查询，但是会在结果中被过滤掉。


> 物理删除索引：
>
> 当索引数据不断增长时，对应的segment也会不断的增多，查询性能可能就会下降。因此，Elasticsearch会触发segment合并的线程，把很多小的segment合并成更大的segment，然后删除小的segment，当这些标记为删除的segment不会被复制到新的索引段中，也就相当于删除了。

#### **12.3 查询文档的原理**

- 查询请求发给任意一个节点，该节点就成了coordinating node，该节点使用路由算法算出文档所在的primary shard
- 协调节点把请求转发给primary shard也可以转发给replica shard(使用轮询调度算法(Round-Robin Scheduling，把请求平均分配至primary shard 和replica shard) 
- 处理请求的节点把结果返回给协调节点，协调节点再返回给应用程序

Elasticsearch中的查询主要分为两类，Get请求：通过ID查询特定Doc；Search请求：通过Query查询匹配Doc。

- 对于Search类请求，查询的时候是一起查询内存和磁盘上的Segment，最后将结果合并后返回。这种查询是近实时（Near Real Time）的，主要是由于内存中的Index数据需要一段时间后才会刷新为Segment。
- 对于Get类请求，查询的时候是先查询内存中的TransLog，如果找到就立即返回，如果没找到再查询磁盘上的TransLog，如果还没有则再去查询磁盘上的Segment。这种查询是实时（Real Time）的。这种查询顺序可以保证查询到的Doc是最新版本的Doc，这个功能也是为了保证NoSQL场景下的实时性要求。

**注意：**在特殊情况时，请求的文档还在建立索引的过程中，primary shard上存在，但replica shar上不存在，但是请求被转发到了replica shard上，这时就会提示找不到文档。

[ElasticSearch的基本原理与用法](https://www.cnblogs.com/luxiaoxun/p/4869509.html)

[elasticsearch学习笔记--原理介绍](https://www.cnblogs.com/zwt1990/p/7737747.html)

[Elasticsearch原理（三）：写入流程](https://blog.csdn.net/xiaoyu_BD/article/details/81950081)

### **13. 写一致性原理和quorum机制**

#### **13.1 写一致性原理**

任何一个增删改操作都可以跟上一个参数`consistency`，可以给该参数指定的值：

* one: (primary shard)只要有一个primary shard是活跃的就可以执行

* all: (all shard)所有的primary shard和replica shard都是活跃的才能执行

* quorum: (default) 默认值，大部分shard是活跃的才能执行 （例如共有6个shard，至少有3个shard是活跃的才能执行写操作）

#### **13.2 quorum机制**

多数shard都是可用的

```
int((primary+number_of_replica)/2)+1
```

例如：3个primary shard，1个replica

int((3+1)/2)+1=3

至少3个shard是活跃的

**注意：**可能出现shard不能分配齐全的情况

比如：1个primary shard,1个replica

int((1+1)/2)+1=2

但是如果只有一个节点，因为primary shard和replica shard不能在同一个节点上，所以仍然不能执行写操作。

再举例：1个primary shard,3个replica,2个节点

int((1+3)/2)+1=3

**注意：**当活跃的shard的个数没有达到要求时，es默认会等待一分钟，如果在等待的期间活跃的shard的个数没有增加，则显示timeout。

```
// 设置超时时间
put /index/type/id?timeout=60s
```

[ES写一致性原理以及quorum机制深入剖析](https://www.jianshu.com/p/1ffaddefb064)

### **14. bulk批量操作的json格式解析**

```
// bulk的格式：

{action:{metadata}}
{requstbody}
```

**为什么不使用如`[{"action": {},"data": {}}]`格式?**

这种方式可读性好，但是内部处理就麻烦了：

* 将json数组解析为JSONArray对象，在内存中就需要有一份json文本的拷贝，另外还有一个JSONArray对象。

* 解析json数组里的每个json，对每个请求中的document进行路由

* 为路由到同一个shard上的多个请求，创建一个请求数组

* 将这个请求数组序列化

* 将序列化后的请求数组发送到对应的节点上去

这样耗费更多内存，增加java虚拟机开销，原来的格式的好处：

* 不用将其转换为json对象，直接按照换行符切割json，内存中不需要json文本的拷贝

* 对每两个一组的json，读取meta，进行document路由

* 直接将对应的json发送到node上去

### **15. 查询结果分析**

```
{
  "took": 419,
  "timed_out": false,
  "_shards": {
    "total": 3,
    "successful": 3,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 3,
    "max_score": 0.6931472,
    "hits": [
      {
        "_index": "lib3",
        "_type": "user",
        "_id": "3",
        "_score": 0.6931472,
        "_source": {
          "address": "bei jing hai dian qu qing he zhen",
          "name": "lisi"
        }
      },
      ....
      
```

* took：查询耗费的时间，单位是毫秒。

* _shards：共请求了多少个shard。

* total：查询出的文档总个数。

* max_score： 本次查询中，相关度分数的最大值，文档和此次查询的匹配度越高，_score的值越大，排位越靠前。

* hits：默认查询前10个文档。

* timed_out：是否查询超时。

### **16. 多index，多type查询模式**

```
GET _search

GET /lib/_search

GET /lib,lib3/_search

GET /*3,*4/_search

GET /lib/user/_search

GET /lib,lib4/user,items/_search

GET /_all/_search

GET /_all/user,items/_search
```

### **17. 分页查询中的deep paging问题**

```
GET /lib3/user/_search
{
    "from":0,
    "size":2,
    "query":{
        "terms":{
            "interests": ["hejiu","changge"]
        }
    }
}

GET /_search?from=0&size=3
```

deep paging:查询的很深，比如一个索引有三个primary shard，分别存储了6000条数据，我们要得到第100页的数据(每页10条)，类似这种情况就叫deep paging

（1）如何得到第100页的10条数据？

在每个shard中搜索990到999这10条数据，然后用这30条数据排序，排序之后取10条数据就是要搜索的数据，这种做法是错的，因为3个shard中的数据的`_score`分数不一样，可能这某一个shard中第一条数据的`_score`分数比另一个shard中第1000条都要高，所以在每个shard中搜索990到999这10条数据然后排序的做法是不正确的。

正确的做法是每个shard把0到999条数据全部搜索出来（按排序顺序），然后全部返回给coordinate node，由coordinate node按_score分数排序后，取出第100页的10条数据，然后返回给客户端。

（2）deep paging性能问题

* 耗费网络带宽，因为搜索过深的话，各shard要把数据传送给coordinate node，这个过程是有大量数据传递的，消耗网络。

* 消耗内存，各shard要把数据传送给coordinate node，这个传递回来的数据，是被coordinate node保存在内存中的，这样会大量消耗内存。

* 消耗cpu，coordinate node要把传回来的数据进行排序，这个排序过程很消耗cpu。

鉴于deep paging的性能问题，所以应尽量减少使用。

### **18. query string查询及copy_to解析**

#### **18.1 query string查询**

（1）查询条件`q`参数用于指定返回的文档必须匹配的查询条件。

```
// 1.指定搜索title字段中包含azure关键字的文档
q=title:azure

// 2.不指定字段
q=azure
```

（2）可以设置一个字段包含多个关键字，关键字之间使用空格或逗号分隔。

```
// 1.只要title字段包含任意一个关键字，文档就满足查询条件
q=title:(azure,aws,cloud)
// 或 
q=title:(azure aws cloud)
```

（3）q参数可以指定搜素一个短语，短语使用双引号标识。

```
// 指定搜索title中包含短语“azure vs aws”的文档
q=title:"azure vs aws"
```

（4）在查询条件中，也可以指定操作符：`+`或`-`，操作符 `+` 用于指定返回的文档必须匹配查询条件；操作符 `-` 用于指定返回的文档不匹配查询条件；操作符之间以空格分隔，操作符是位于查询条件=号右侧，字段前面。

```
// 指定搜索字段title中只能包含azure，不能包含aws
q=+title:azure -title:aws
```

[ElasticSearch查询 第一篇：搜索API](https://www.cnblogs.com/ljhdo/p/4486978.html)

#### **18.2 copy_to解析**

copy_to字段是把指定字段中的值，以空格为分隔符组成一个大字符串，然后被分析和索引，但是不存储。也就是说它能被查询，但不能被取回显示。

```
// 将title字段的值加入到fullcontents字段中
PUT /zpark/_mapping
{
	"mappings" : {
		"properties" : {
			"title" : {
				"type" : "text",
				// 指定复制到的字段
				"copy_to" : "fullcontents"
			}
		}
	}
}
```

**注意：**copy_to指向的字段字段类型要为`text`。

当没有指定field时，就会从copy_to字段中查询，可以提高性能，不用去查询全部的字段。

```
GET /lib3/user/_search?q=changge
```

### **19. 字符串排序问题**

对一个字符串类型的字段进行排序通常不准确，因为已经被分词成多个词条了。解决方式为：对字段索引两次，一次索引分词（用于搜索），一次索引不分词(用于排序)。

```
// 创建新的mapping
PUT /lib3
{
    "settings":{
        "number_of_shards" : 3,
        "number_of_replicas" : 0
      },
     "mappings":{
      "user":{
        "properties":{
            "name": {"type":"text"},
            "address": {"type":"text"},
            "age": {"type":"integer"},
            "birthday": {"type":"date"},
            "interests": {
                "type":"text",
                // 在其中建一个keyword类型字段
                "fields": {
                  "raw":{
                     "type": "keyword"
                   }
                },
                // fielddata设置为true
                "fielddata": true
             }
          }
        }
     }
}

// 对interest字段进行升序排序
GET /lib3/user/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "interests.raw": {
        "order": "asc"
      }
    }
  ]
}
```

**注意：**如果是按照上面创建mapping后，使用interests进行排序，则还是会对分词后的字符串进行排序。需要对interests.raw进行排序，因为这是keyword类型，不进行分词，而是对整改字符串进行排序比较

### **20. 如何计算相关度分数**

使用的是TF/IDF算法(Term Frequency&Inverse Document Frequency)

（1）Term Frequency：我们查询的文本中的词条在一个document中出现了多少次，出现次数越多，相关度越高。

```
搜索内容： hello world

Hello，I love china.

Hello world,how are you!

索引词条在第二条文档出现两次，第二条文档的tf得分高
```

（2）Inverse Document Frequency：我们查询的文本中的词条在索引的所有文档中出现了多少次，出现的次数越多，相关度越低。

```
搜索内容：hello world

hello，what are you doing?

I like the world.

假如hello 在索引的所有文档中出现了500次，world出现了100次，这样第二条文档的idf得分更高
```

（3）Field-length norm(字段长度归约)：field越长，相关度越低

```
搜索内容：hello world

{"title":"hello,what's your name?","content":{"owieurowieuolsdjflk"}}

{"title":"hi,good morning","content":{"lkjkljkj.......world"}}

第二条的content字段长度比第一条字段的title字段值长，所以第一条fln得分高
```

（4）查看分数是如何计算的

```
GET /lib3/user/_search?explain=true
{
    "query":{
        "match":{
            "interests": "duanlian,changge"
        }
    }
}
```

（5）查看一个文档能否匹配上某个查询，指定id符合查询条件

```
GET /lib3/user/2/_explain
{
    "query":{
        "match":{
            "interests": "duanlian,changge"
        }
    }
}
```

### **21. Doc_Values 解析**

Doc_Values其实是Lucene在构建倒排索引时，会额外建立一个有序的正排索引(基于document => field value的映射列表)。存储在磁盘上节省内存 ，对排序、分组和一些聚合操作能够大大提升性能 。

```
{"birthday":"1985-11-11",age:23}

{"birthday":"1989-11-11",age:29}
```

会建立成这样的形式的有序映射列表：

document     age       birthday

doc1             23         1985-11-11

doc2             29         1989-11-11

```
PUT /lib3
{
    "settings":{
    "number_of_shards" : 3,
    "number_of_replicas" : 0
    },
     "mappings":{
      "user":{
        "properties":{
            "age": {
              "type":"integer",
              "doc_values":false
            }
        }
      }
     }
}
```

**注意：**

* 默认对不分词的字段是开启的，对分词字段无效（需要把fielddata设置为true）。
* 关闭后，会对排序产生影响，就不能排序了。

### **22. 基于scroll技术滚动搜索大量数据**

如果一次性要查出来比如10万条数据，那么性能会很差，此时一般会采取用scoll滚动查询，一批一批的查，直到所有数据都查询完为止。

* scoll搜索会在第一次搜索的时候，保存一个当时的视图快照，之后只会基于该旧的视图快照提供数据搜索，如果这个期间数据变更，是不会让用户看到变化的。

* 采用基于`_doc`(不使用_score)进行排序的方式，性能较高

* 每次发送scroll请求，我们还需要指定一个scoll参数，指定一个时间窗口，每次搜索请求只要在这个时间窗口内能完成就可以了 

```
// 指定第一批查询在1分钟内查完 
GET /lib3/user/_search?scroll=1m
{
  "query": {
    "match_all": {}
  },
  // 不按照分数排序，按照id排序，提高性能
  "sort":["_doc"],
  "size":3
}

// 第二批查询是接着第一批查询的scroll_id后查询的
GET /_search/scroll
{
   "scroll": "1m",
   "scroll_id": "DnF1ZXJ5VGhlbkZldGNoAwAAAAAAAAAdFkEwRENOVTdnUUJPWVZUd1p2WE5hV2cAAAAAAAAAHhZBMERDTlU3Z1FCT1lWVHdadlhOYVdnAAAAAAAAAB8WQTBEQ05VN2dRQk9ZVlR3WnZYTmFXZw=="
}
```

### **23. 创建类型时的几种属性**

#### **23.1 dynamic mapping策略**

当ES在文档中碰到一个以前没见过的字段时，它会利用动态映射来决定该字段的类型，并自动地对该字段添加映射。可以通过dynamic设置来控制这一行为，dynamic设置可以适用在根对象上或者object类型的任意字段上。它能够接受以下的选项：

- true：默认值。动态添加字段
- false：忽略新字段
- strict：如果碰到陌生字段，抛出异常

```
PUT /lib8
{
    "settings":{
    "number_of_shards" : 3,
    "number_of_replicas" : 0
    },
     "mappings":{
      "user":{
        "dynamic":strict,
        "properties":{
            "name": {"type":"text"},
            "address":{
                "type":"object",
                "dynamic":true
            },
        }
      }
     }
}

// 下面会报错
PUT  /lib8/user/1
{
  "name":"lisi",
  // 出现了定义以外的age字段，会报错
  "age":20,
  "address":{
    "province":"beijing",
    "city":"beijing"
  }
}
```

#### **23.2 date_detection日期格式识别**

默认会把按照一定格式的字符串识别date，比如yyyy-MM-dd，但是如果不想把字符串默认识别为date，则将date_detection关闭。除此之外还有numeric_detection，为数字检测，用法和意思类似，不作表述。

```
// 可以手动关闭某个type的date_detection
PUT /lib8
{
    "settings":{
    "number_of_shards" : 3,
    "number_of_replicas" : 0
    },
     "mappings":{
      "user":{
        "date_detection": false,
        }
    }
}
```

#### **23.3 定制 dynamic mapping template(type)**

```
PUT /my_index
{ 
  "mappings": { 
    "my_type": { 
      "dynamic_templates": [ 
        { 
          // 一个模板的名字，任意起
          "en": { 
            "match": "*_en", 
            "match_mapping_type": "string", 
            "mapping": { 
              "type": "text", 
              "analyzer": "english" 
            } 
          } 
        } 
      ] 
     } 
  } 
}

#使用了模板
PUT /my_index/my_type/3
{
  "title_en": "this is my dog"
}

#没有使用模板
PUT /my_index/my_type/5
{
  "title": "this is my cat"
}

GET my_index/my_type/_search
{
  "query": {
    "match": {
      "title": "is"
    }
  }
}
```

[ES6.2.4学习-动态映射解析](https://blog.csdn.net/zhanghytc/article/details/80667150)

### **24. 重建索引**

#### **24.1 重建index的步骤**

一个字段的数据类型设置是不能修改的，如果要修改一个字段的数据类型，那么应该重新按照新的mapping，建立一个index，然后将数据批量查询出来，重新用bulk api写入到index中。批量查询的时候，建议采用scroll api，并且采用多线程并发的方式来reindex数据，每次scroll就查询指定日期的一段数据，交给一个线程即可。

```
// 第一次插入值时，会自动检测content数据类型为date
PUT /index1/type1/4
{
   "content":"1990-12-12"
}

// 第二次再插入一条String类型的content，会报错
PUT /index1/type1/4
{
   "content":"I am very happy."
}

// 不允许修改index的mapping，会报错
PUT /index1/_mapping/type1
{
  "properties": {
    "content":{
      "type": "text"
    }
  }
}
```

以上方法都会报错，因此需要重新建立一个索引，把index1索引中的数据查询出来导入到新的索引中。但是java远程应用程序使用的是之前的索引，为了不用重启应用程序，给index1这个索引起个别名。

```
// 1.给index1起别名为index2
PUT /index1/_alias/index2

// 2.创建新的索引，把content的类型改为字符串
PUT /newindex
{
  "mappings": {
    "type1":{
      "properties": {
        "content":{
          "type": "text"
        }
      }
    }
  }
}

// 3.使用scroll批量查询
GET /index1/type1/_search?scroll=1m
{
  "query": {
    "match_all": {}
  },
  "sort": ["_doc"],
  "size": 2
}

// 4.使用bulk批量写入新的索引
POST /_bulk
{"index":{"_index":"newindex","_type":"type1","_id":1}}
{"content":"1982-12-12"}

// 5.将别名index2和新的索引关联，应用程序不用重启
POST /_aliases
{
    "actions": [
    	// 将旧的索引和别名取消关联
        {"remove": {"index":"index1","alias":"index2"}},
        // 将新的索引和别名增加关联
        {"add": {"index": "newindex","alias": "index2"}}
    ]
}

// 6.查询是否成功
GET index2/type1/_search
```

#### **24.2 索引不可变的原因**

**倒排索引包括：**文档的列表，文档的数量，词条在每个文档中出现的次数，出现的位置，每个文档的长度，所有文档的平均长度。

**索引不变的原因：**不需要锁，提升了并发性能；可以一直保存在缓存中（filter）；节省cpu和io开销。

## 五、在Java应用中访问ElasticSearch

### **1. 引入依赖**

环境准备：es为6.2.4；jdk为1.8；junit4。

```
// pom中加入ElasticSearch6.2.4的依赖
<dependencies>
    <dependency>
      <groupId>org.elasticsearch.client</groupId>
      <artifactId>transport</artifactId>
      <version>6.2.4</version>
    </dependency>    
    
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
</dependencies> 

  <build>
      <plugins>
			<!-- java编译插件 -->
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.2</version>
				<configuration>
					<source>1.8</source>
					<target>1.8</target>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
		</plugins>
  </build>  
```

>  ES提供了两种java的API对数据进行ES集群操作：[TransportClient](https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/client.html)，[Java REST Client](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-overview.html)。
>
> 但是有以下几点需要注意：
>
> 计划在7中删除TransportClient客户端，并在8中完全删除它。
>
> Java REST Client客户端目前支持更常用的API，但仍有很多需要添加的API。
>
> 任何缺失的API都可以通过使用JSON请求和响应体的低级Java REST客户端来实现。
>
> TransportClient即将过时，虽然RestHighLevelClient还不完善，还需要增加新API，但是RestLowLevelClient非常完善，满足我们的API需求。但是因为本文章是基于视频学习的，所以还是使用TransportClient。

**注意：**不同版本之间创建 client 的方式的差别还是比较大的，所以想要深度学习还需要在网上搜索最新的es教程。

[elasticsearch系列七：ES Java客户端-Elasticsearch Java client）](https://www.cnblogs.com/leeSmall/p/9218779.html)

[ES （Elasticsearch)--Java REST Client解析](https://blog.csdn.net/aA518189/article/details/88956889)

[Elasticsearch JAVA API和JAVA REST Clien比较](https://blog.csdn.net/qq_23146763/article/details/69370929)

### **2. 连接elasticsearch**

```java
// 1.指定es集群，vi ./elasticsearch.yml中查看集群的名称和节点名称
Settings settings = Settings.builder().put("cluster.name", "集群名称").build();
TransportClient client = new PreBuiltTransportClient(settings);

// 2.创建访问es节点服务器的客户端
TransportClient client = new PreBuiltTransportClient(Settings.EMPTY)
        .addTransportAddress(new TransportAddress(InetAddress.getByName("IP地址1"), 9300))
        .addTransportAddress(new TransportAddress(InetAddress.getByName("IP地址2"), 9300));

......
        
// 3.最后要关闭客户端        
client.close();
```

### **3. 在Java应用中实现查询文档** 

```java
// 1.查询twitter索引下的_doc类型下的指定id
GetResponse response = client.prepareGet("twitter", "_doc", "1").get();

// 2.获取json格式的字符串数据
response.getSourceAsString();

// 3.获取map格式的数据
response.getSourceAsMap();
```

### **4. 在Java应用中实现添加文档**

```java
// 1.添加数据
XContentBuilder doc1 = XContentFactory.jsonBuilder()
    .startObject()
    .field("id","3") // 这里的id是id字段
    .field("title","Java设计模式之单例模式")
    .field("content","枚举单例模式可以防反射攻击。")
    .field("postdate","2018-02-03")
    .field("url","csdn.net/79247746")
    .endObject();

// 2.null表示让es自动生成文档id，也可以自己指定
IndexResponse response = client.prepareIndex("twitter", "_doc", null)
    .setSource(doc1)
    .get();

// 3.是否添加成功
System.out.println(response.status());                    
```

### **5. 在Java应用中实现删除文档**

```java
// 1.删除指定的文档id
DeleteResponse response=client.prepareDelete("twitter","_doc","SzYJjWMBjSAutsuLRP_P").get();

// 2.删除成功返回OK，否则返回NOT_FOUND
System.out.println(response.status());
```

### **6. 在Java应用中实现更新文档**

```json
// 1.post方式
UpdateRequest request=new UpdateRequest();
request.index("twitter")
    .type("_doc")
    .id("2")
    .doc(
        XContentFactory.jsonBuilder().startObject()
        .field("title","单例模式解读")// 指定更新的字段
        .endObject()
	);
UpdateResponse response=client.update(request).get();
// 更新成功返回OK，否则返回NOT_FOUND
System.out.println(response.status());

// 2.upsert方式
// 要先指明一个文档，如果没有就添加这个文档
IndexRequest request1 =new IndexRequest("twitter","_doc","3")
                .source(
                		XContentFactory.jsonBuilder().startObject()
                                .field("id","3")
                                .field("title","装饰模式")
                                .field("content","动态地扩展一个对象的功能")
                                .field("postdate","2018-05-23")
                                .field("url","csdn.net/79239072")
                                .endObject()
                );
// 如果有这个文档则执行修改操作，没有就进行添加操作
UpdateRequest request2=new UpdateRequest("index1","blog","3")
.doc(
    XContentFactory.jsonBuilder().startObject()
    .field("title","装饰模式解读")
    .endObject()
).upsert(request1);

UpdateResponse response=client.update(request2).get();    
// upsert操作成功返回OK，否则返回NOT_FOUND
System.out.println(response.status());
```

### **7. 在Java应用中实现批量操作**

#### **7.1 使用mget的方式批量查询**

```java
MultiGetResponse mgResponse = client.prepareMultiGet()
    // 查询指定索引下指定类型的多个文档id
    .add("index1","blog","3","2")
    .add("lib3","user","1","2","3")
    .get();

// 循环输出查询结果
for(MultiGetItemResponse response : mgResponse){
    GetResponse rp = response.getResponse();
    if(rp!=null && rp.isExists()){
    	System.out.println(rp.getSourceAsString());
    }
}	     
```

#### **7.2 使用bulk的方式批量添加**

```java
BulkRequestBuilder bulkRequest = client.prepareBulk();

bulkRequest.add(client.prepareIndex("lib2", "books", "4")
                .setSource(XContentFactory.jsonBuilder()
                        .startObject()
                        .field("title", "python")
                        .field("price", 68)
                        .endObject()
                )
        );

bulkRequest.add(client.prepareIndex("lib2", "books", "5")
                .setSource(XContentFactory.jsonBuilder()
                        .startObject()
                        .field("title", "VR")
                        .field("price", 38)
                        .endObject()
                )
        );
// 批量执行
BulkResponse bulkResponse = bulkRequest.get();
        
System.out.println(bulkResponse.status());
// 判断是否存在失败的操作
if (bulkResponse.hasFailures()) {
    System.out.println("存在失败操作");
}
```

### **8. 查询删除**

```java
BulkByScollResponse response = DeleteByQueryAction.INSTANCE
	.newRequestBuider(client)
	.filter(QueryBuilders.matchQuery("title", "工厂"))// 查询匹配的文档
	.source("index1")// 指定索引
	.get();
// 获取删除的文档数	
long counts = response.getDeleted();
```

### **9. Query查询**

#### **9.1 match查询**

```java
// 查询全部
QueryBuilder queryBuilder = QueryBuilders.matchAllQuery();
// 查询指定字段的指定值
QueryBuilder queryBuilder2 = QueryBuilders.matchQuery("title", "工厂");
// 查询多个字段
QueryBuilder queryBuilder3 = QueryBuilders.multiMatchQuery("工厂", // 值 
                                                           "title", "address");	// 字段

SearchResponse searchResponse = client.prepareIndex("index1")
	.setQuery(queryBuilder)
	.setSize(3)
	.get();
// 获取查询到的所有文档	
SearchHits hits = searchResponse.getHits();   

for(SearchHit hit : hits){
	hit.getSourceAsMap();
}
```

#### **9.2 term和terms查询**

```java
QueryBuilder queryBuilder = QueryBuilders.termQuery("title", "工厂");

QueryBuilder queryBuilder = QueryBuilders.termsQuery("title", // 字段
                                                     "工厂", "工厂2"); // 值
```

#### **9.3 range查询**

```java
QueryBuilder queryBuilder = QueryBuilders.rangeQuery("price")   // 字段
        .from(5)                                                 
        .to(10)                                                  
        .includeLower(true)                                      
        .includeUpper(false); 
```

#### **9.4 prefix查询**

```java
QueryBuilder queryBuilder = QueryBuilders.prefixQuery("title", // 字段
												 "heine"); // 必须匹配的前缀
```

#### **9.5 wildcard查询**

```java
QueryBuilder queryBuilder = QueryBuilders.wildcardQuery("title", 
                                                        "k?mch*"); // 通配符查询
```

#### **9.6 fuzzy查询**

#### **9.7 type查询**

```java
QueryBuilder queryBuilder = QueryBuilders.typeQuery("my_type"); // 查询指定的index下的指定类型
```

#### **9.8 ids查询**

```java
QueryBuilder queryBuilder = QueryBuilders.idsQuery().addIds("1", "4", "100"); // 指定的多个id
```

#### **9.9 regex查询**

```java
QueryBuilder queryBuilder = QueryBuilders.regexpQuery( "name.first", 
                                                      "s.*y"); // 正则表达式
```

#### **9.10 exists查询**

```java
QueryBuilder queryBuilder = QueryBuilders.existsQuery("name");  
```

### **10.聚合查询**              

#### **10.1 max最大值**

```java
MaxAggregationBuilder aggregation = AggregationBuilders
    .max("agg") // 最大值的字段
    .field("height"); // 聚合的字段

SearchResponse searchResponse = client.prepareIndex("index1").addAggregation(aggregation).get();
// 获取最大值的字段agg
Max max = searchResponse.getAggregations().get("agg");
// 获取值
max.getValue(); 
```

#### **10.2 stat统计**

```java
ExtendedStatsAggregationBuilder aggregation = AggregationBuilders
    .extendedStats("agg")
    .field("height");

SearchResponse searchResponse = client.prepareIndex("index1").addAggregation(aggregation).get();
// 获取最大值的字段agg
Max max = searchResponse.getAggregations().get("agg");
// 获取各个聚合的值
double min = agg.getMin();
double max = agg.getMax();
double avg = agg.getAvg();
double sum = agg.getSum();
long count = agg.getCount();
double stdDeviation = agg.getStdDeviation();
double sumOfSquares = agg.getSumOfSquares();
double variance = agg.getVariance();
```

### **11. query string 查询**

```java
QueryBuilder queryBuilder = QueryBuilders.queryStringQuery("+change -hejiu");
// 满足其中一个条件就可以查出来
QueryBuilder queryBuilder = QueryBuilders.simpleQueryStringQuery("+change -hejiu");
```

### **12. 组合查询** 

#### **12.1 bool方式**

```java
QueryBuilder queryBuilder = QueryBuilders.boolQuery()
	.must(QueryBuilders.matchQuery("title", "工厂")) // 必须有工厂
	.mustNot(QueryBuilders.matchQuery("title", "工厂2")) // 必须没有工厂2
	.should(QueryBuilders.matchQuery("title", "工厂3")) // 应该有工厂3
    .filter(QueryBuilders.rangeQuery("price").gte("20")); // 价格大于20
```

#### **12.2 constant score方式**

```java
// 将分数设置为常数，因此查询出来的结果排序与分数无关
QueryBuilder queryBuilder = QueryBuilders.constantScoreQuery(QueryBuilders.termQuery("title", "工厂"));
```

## 六、其他相关扩展学习

### **1. ElasticSearch集群搭建**

[ElasticSearch7.1.1集群搭建](https://blog.csdn.net/qq_27512271/article/details/98740135)

[elasticsearch7 集群搭建带安全验证](https://blog.csdn.net/yonggeit/article/details/90270978)

## 参考：

[随笔分类 - 搜索引擎](https://www.cnblogs.com/leeSmall/category/1210814.html)

```
// 基本命令的整合
PUT /my_index/{
	"mappings":{
		"properties":{
			"name":{
				"type":"text",
				"fields":{
					"raw":{
						"type":"keyword",	
					}					
				}
				"analyzer
				"boost"
				"copy_to"
				"format"
				"fields"
			}
		}
	}
}

动态映射

禁止相关的检测
PUT /my_index
{
	"mapping":{
		"_doc":{
			注意:这是文档的属性
			"data_detection":false,
			"numberic_detection":false
			"properties":{
	
			}
		}
	}
}

建立索引时定义索引别名:
PUT /my_index
{
"aliases":{
	"aliases1":{
		
}
	"aliases2":{
		filters:{
			"term":{
				"name":"zs"
			}
		}
	}
}
}

后创建别名,把索引对应上别名
POST /_aliases
{
	"actions":{
		"add":{
			"index":"text*"  可以进通配符匹配,
			"alias":"alias1",
			"routing":"1"指定分片
			"filter":{
				"term":{
					"name":"zs"
				}
			}
		}
		"remove":{}
	}
}

PUT /user/alias/alias1
{
	"routing":"1",
	"filter":{
		term:{
			"name":"zs"
		}
	}
}

GET /index名字
GET /_template/
GET /_alias

上一篇主要学习了 index 创建\删除\修改设置\查询
创建索引中  settings 下的 index 设置分片信息\映射信息\设置索引读写权限
删除索引
修改索引 也是修改 settings 下的 index 
查询索引 获取 设置信息\映射信息\

创建索引模板
PUT /_template/template1
{
	"index_patterns":["index1","index1*"]
	下面的部分和正常的创建索引一样
}

查看索引模板 _template/"index_patterns"

打开和关闭索引 POST/index/_close

收缩分片为原本的因子数

基本步骤:原索引分片转移到一个指定的节点(需要自己进行配置),阻止写操作(配置),
将原本索引复制到指定主分片数的索引下,

POST /index1/_shrink/_index2
{
	"settings":{
		"number_of_shards":2
	}
}

split 切割的过程,只需要关闭写操作blocks,
POST /index/_split/index2
{
	"settings":{
		"number_of_shards":4
	}
}

mapping的学习

multi_fields  对一个字段 定义多个类型 方便查询

{
	"name":{
		"type":"text",
		"fields":{
			"raw1":{
				type:"keywords"
			}
		}	
	}
}


对一个文档进行操作, data_detection,dynamic 是否动态映射
都一个自动操作:字段类型,多值fields,

索引别名的操作:
aliases

在创建索引的时候创建别名 
aliases

POST /_aliases
{
	"actions":{
		"add":{ "index":"通配符", alias:"索引名",filter:{},routing:设置指定的分片}
		"remove":{}
	}
}

PUT /users/_alias/users2
{
	
}

查看别名信息
GET _alias


分词器: 字符过滤器(可以先对字符进行一些过滤操作),分词器,分词过滤器(指定同义词和停用词过滤器)组成(知道有什么用)

文档管理
新建有指定id更新(PUT), 指定id(POST)
获取文档:
单个获取,通过id获取,
GET ../../1

GET ../../_mget
{
 ids:["", "", ""]
}

GET ../../_mget
{
	"docs":{
		_index
		_type
		_id:
	}
}

通过id删除
通过查询删除

通过id更新
通过查询更新
通过脚本更新
POST /index1/_doc/1/_update
{
"script":{
	"source":"""
		进行多条语句的操作,
		ctx._source.字段 进行操作
	""",
	lang:"painless",
	"params":{
		这里面定义一些值可以让source中的语句使用
	}
}
"upsert":{
	这里是upsert的文档
}
}
通过文档更新
POST /index1/_doc/1/_update
{
	"doc":{
		name:"ls"
	},
	"detect_noop":false
	"doc_as_upsert":true,
}

POST /index1/_doc/_update_by_query
{
	"script":{
		"source":"""

		""",
	}
	query:{
	
	}
}

POST /index1/_doc/_bulk
{"index":{"_id":1}},
{键值对数据}

注意:这里是重索引的补充
POST /_reindex
{
"size":10,//只查询十条数据
"source":{
	index:index1,
	"query":{
	满足条件
}
},
"dest":{
index:index2
}
,"script":{

}
}

查询指定的值,对查询后的值进行运算再输出
GET /index1/_search
{
	// from 和 size 可以实现分页,注意深度分页的问题
	from:1,
	size:20,
	"_source":[选择部分字段进行查询],
	min_score:1,//对最低分进行限制
	"query":{
		
	},
	// 对获取的文档再过滤一遍
	"post_filter":{
		"term":{
			"name":"zs"
		}
	},
	// 这里面可以对多个字段进行排序
	sort:{
		"age":{
			"order":"desc"
		},
		// 也可以对数组进行取值后进行排序
		"price":{
			"order":"desc",
			"mode":"avg"
		},
	},
	"script_fields":{
		"运算后返回的新的字段名":{
			脚本命令
		}
	},
	// 折叠结果,会在查询的结果中特别的显示
	"collapse":{
		"field":"age"	
	},
	// 高亮搜索到term
	"hightlight":{
		"content":{
			"pre_tags":"<em>",
			"post_tags":"<em>"
		}
	}
}
// 获取查询结果的条数
GET /index1/_doc/_count
{
	"query":{
		
	}
}

DSL 语言
叶子语句可以单独使用,也可以结合使用,match,term,range

GET /index1/_search
{
	"query":{
		"bool":{
			"must":{
				"match":{
					"term":{
						"name":"zs"
					}
				}
			},
			"must_not":{
				match":{
					term":{
						"age":24
					}
				}
			},
			"filter":{
				"term":{
					"person":"student"
				}
			}
		}
	}
}

match_all
"query":
{
	"match_all":{}
}

match
"query":
{
	"match":{
		"title":{
			"query":"lucene java",
			"operate":"and",
			"fuzziness":2,
			"minimum_should_match":2
		}
	}
}

match_phrases
"query":{
	"match_phrases":{
		"title":{
			"query":"lucene java",
			"fuzziness":2,
			"slop":2
		}
	}
}

multi_match
"query":
{
	"multi_match":{
		"query":"java",
		"fields":["name","title"]
	}
}

相关分数计算
term frequency
inverse document frequency
field length norm 

query_string (使用lucene查询语法查询)
"query":
{
	"query_string":{
		"fields":["name","titl*"],
		"query":"java AND lucene"
	}
}

term的分词
term
"query":
{
	"term":{
		"name":{
			"value":"zs",
			"boost":2
		}
	}
}

terms
"query":
{
	"terms":{
		"name":["zs","ls"]
	}
}

range
"query":{
	"range":{
		"age":{
			"gte":20,
			"lt":24
		}
	}
}

exists
"query":
{
	"exists":{
		"field":"user"
	}
}

prefix
"query":{
	"prefix":{
		"name":{
			"value":"z"
		}
	}
}

wildcard
"query":
{
	"wildcard":{
		"name":{
			"value":"z*"
		}
	}
}

regexp
"query":
{
	"regexp":{
		"name":"s.*y"
	}
}

聚合查询
聚合分为指标聚合和桶聚合(指标聚合可以建立在桶聚合的基础上)
"aggs":
{
	"max_fields":{
		"max":{
			"field":"balance",
			// 这里面的字段名也可以替换成脚本
		}
	}
}

max,min,avg,sum,cardinality,stat

桶聚合
terms 桶聚合,对指定字段的每个值进行聚合,返回值和所对应的数量
"aggs":
{
	"term_aggs":{
		"range":{
			"fields":"age"
			"ranges":[
				{
					"to":25
				},
				{
					"from":26
					"to":30
				},
				{
					"from":31
				}
			]
		}
		"filter":{
			"match":{
				"title":"f"
			}
		},
		"terms":{
			"field":"age",
			"size":"10,
			"include":["value1","value2"],
			"exclude":["value3"]
			"order":{
				"_key":"asc"
			}
			// 上面的指令也可以替换成script脚本
		},
		"aggs":{
			"min_field":{
				"min":{
					"field":"balance"
				}
			}
		}
	}
}
```

