# Lucene学习

## 一、基本概念

### **1.Lucene是什么**

Lucene是apache旗下的顶级开源项目，是一个可以创建全文检索引擎系统的**全文检索工具包**（一堆jar包），提供了完整的查询引擎和索引引擎，部分文本分析引擎。Lucene的目的是为软件开发人员提供一个简单易用的工具包，以方便的在目标系统中实现全文检索的功能。可以使用它来构建全文检索引擎系统，但是它不能独立运行，需要放在tomcat下才可以独立运行，对外提供全文检索服务。

### **2.数据的基本分类**

* **结构化数据：**有固定类型或者有固定长度的数据。例如:数据库中的数据（mysql、oracle等）， 元数据（描述数据的数据，可以简单的看成windows中的数据）。

* **结构化数据搜索方法：**

  *   数据库中数据通过sql语句可以搜索

    *   元数据（windows中的数据通过windows提供的搜索栏进行搜索）
  
* **非结构化数据：**没有固定类型和固定长度的数据。例如: world文档中的数据， 邮件中的数据。

* **非结构化数据搜索方法：**
  * **顺序扫描法：** 拿到搜索的关键字，去文档中逐字匹配，直到找到和关键字一致的内容为止。优点：如果文档中存在要找的关键字就一定能找到想要的内容；缺点： 效率低。
  	
    	* **全文检索算法（倒排索引算法）：**将文件中的内容提取出来， 将文字拆封成一个一个的词（**分词**），将这些词组成**索引**（类似于字典中的目录）， 搜索的时候先搜索索引，通过索引找文档，这个过程就叫做全文检索。

### **3.分词和索引的概念**

* **分词**：去掉停用词（a， an， the ，的， 地， 得， 啊， 嗯 ，呵呵），因为搜索的时候搜索这些词没有意义，将句子拆分成词，去掉标点符号和空格。优点：搜索速度快。缺点：因为创建的索引需要占用磁盘空间，所以这个算法会使用掉更多的磁盘空间（**空间换时间**）。

* **索引：**相当于字典，分为目录和正文两部分，查询的时候通过先查目录，然后通过目录上标注的页数去正文页查找需要的内容。

### **4.Lucene应用领域**

*  互联网全文检索引擎（比如百度，  谷歌，  必应（必应是微软的搜索引擎））

* 站内全文检索引擎（淘宝， 京东搜索功能）

* **优化数据库查询**（因为数据库中使用like关键字是全表扫描也就是顺序扫描算法，查询慢）。

### **5.爬虫是什么**

从互联网上、数据库、文件系统中等获取需要搜索的原始信息，这个过程就是信息采集，信息采集的目的是为了对原始内容进行索引。**在Internet上采集信息的软件通常称为爬虫或蜘蛛，也称为网络机器人**，爬虫访问互联网上的每一个网页，将获取到的网页内容存储起来。**Lucene不提供信息采集的类库**，如果需要测试的数据源需要自己编写一个爬虫程序实现信息采集，也可以通过一些开源软件实现信息采集，如下：

* [Nutch](http://lucene.apache.org/nutch)：Nutch是apache的一个子项目，包括大规模爬虫工具，能够抓取和分辨web网站数据。

* [jsoup](http://jsoup.org/ )：jsoup 是一款Java 的HTML解析器，可直接解析某个URL地址、HTML文本内容。它提供了一套非常省力的API，可通过DOM，CSS以及类似于jQuery的操作方法来取出和操作数据。

* [heritrix](http://sourceforge.net/projects/archive-crawler/files/)：Heritrix 是一个由 java 开发的、开源的网络爬虫，用户可以使用它来从网上抓取想要的资源。其最出色之处在于它良好的可扩展性，方便用户实现自己的抓取逻辑。

## 二、Lucene实现全文检索的流程

### **1.基本概念**

* **索引:**`域名:词`这样的形式，它里面有指针执行这个词来源的文档

* **索引库**：放索引的文件夹(这个文件夹可以自己随意创建，在里面放索引就是索引库)

* **Term词元**：就是一个词，是lucene中词的最小单位，**不同的域中拆分出来的相同的单词是不同的term**。term中包含两部分一部分是文档的域名，另一部分是单词的内容。例如：文件名中包含apache和文件内容中包含的apache是不同的term。

* **文档：**Document对象，一个Document中可以有多个Field域对象，Field域对象中是key-value键值对的形式，有域名和域值，一个document就是数据库表中的一条记录， 一个Filed域对象就是数据库表中的一行一列，这是一个通用的存储结构。

### **2.索引和搜索流程**

![索引和搜索流程](.\Lucene\索引和搜索流程图.png)

* 索引过程，对要搜索的原始内容进行索引构建一个索引库，索引过程包括：确定原始内容即要搜索的内容->采集文档->创建文档对象->分析文档->创建索引用来索引文档。

* 搜索过程，从索引库中搜索内容，搜索过程包括：用户通过搜索界面->创建查询->执行搜索->从索引库搜索->渲染搜索结果。

### **3.创建索引**

对文档索引的过程，将用户要搜索的文档内容进行索引，索引存储在索引库（index）中。**以搜索磁盘上的文本文件为例**：凡是**文件名或文件内容包括关键字的文件**都要找出来，这里要对文件名和文件内容创建索引。

**2.1 获得原始文档**：原始文档是指要索引和搜索的内容，包括互联网上的网页、数据库中的数据、磁盘上的文件等。 本案例我们要获取磁盘上文件的内容，可以通过文件流来读取文本文件的内容，对于pdf、doc、xls等文件可通过第三方提供的解析工具读取文件内容，比如Apache POI读取doc和xls的文件内容。

![原始文档](.\Lucene\原始文档.png) 

**2.2 创建文档对象**：获取原始内容的目的是为了索引，在索引前需要将原始文件创建成文档（原始内容放在文件系统中，创建成文档后会将相关信息存储到Lucene的Document中），文档中包括一个一个的域（Field），域中存储原始文件相关的信息内容。这里我们可以将磁盘上的一个文件当成一个Document（还没有存储到Lucene中的Document），Document中包括一些Field（file_name文件名称、file_path文件路径、file_size文件大小、file_content文件内容等原始文件的相关信息），并将文档对象放入索引库中，如下图：

![创建文档对象](.\Lucene\创建文档对象.png) 	

* 注意：每个Document可以有多个Field，不同的Document可以有不同的Field，同一个Document可以有相同的Field（域名和域值都可以相同），每个文档都有一个唯一的编号，就是文档id。

**2.3 分析文档**：将原始内容创建为包含域（Field）的文档（document），需要再对域中的内容进行分析，分析的过程是经过对原始文档提取单词、将字母转为小写、去除标点符号、去除停用词等过程生成最终的语汇单元，可以将语汇单元理解为一个一个的单词。

```
// 比如下边的文档经过分析如下：

// 1.原文档内容：
Lucene is a Java full-text search engine.  Lucene is not a complete
application， but rather a code library and API that can easily be used
to add search capabilities to applications.

// 2.分析后得到的语汇单元：
lucene、java、full、search、engine.....
```

**2.4 创建索引**：对所有文档分析得出的语汇单元进行索引，索引的目的是为了搜索，最终要实现只搜索被索引的语汇单元从而找到Document（文档）。

![倒排索引结构](.\Lucene\倒排索引结构.png)

* **注意：**

  * 创建索引是对语汇单元索引，通过词语找文档，这种索引的结构叫**倒排索引结构**，**倒排索引结构也叫反向索引结构，包括索引和文档两部分，索引即词汇表，它的规模较小，而文档集合较大。**。传统方法是根据文件找到该文件的内容，在文件内容中匹配搜索关键字，这种方法是顺序扫描方法，数据量大、搜索慢。

  * 创建索引和搜索索引时所用的分词器必须一致。

### **3.查询索引**

查询索引也是搜索的过程。搜索就是用户输入关键字，从索引（index）中进行搜索的过程。根据关键字搜索索引，根据索引找到对应的文档，从而找到要搜索的内容（案例中指磁盘上的文件）。 

**3.1 用户查询接口**：全文检索系统提供用户搜索的界面供用户提交搜索的关键字，搜索完成展示搜索结果。

![用户查询接口](.\Lucene\用户查询接口.png) 

* 注意：Lucene不提供制作用户搜索界面的功能，需要根据自己的需求开发搜索界面。

**3.2 创建查询**：用户输入查询关键字执行搜索之前需要先构建一个查询对象，查询对象中可以指定查询要搜索的Field文档域、查询关键字等，查询对象会生成具体的查询语法。

```
语法 “fileName:spring.txt”表示要搜索Field域的内容为“spring.txt”的文档

语法 “lucene AND java” 表示要搜索即包括关键字“lucene”也包括“java”的文档。
```

**3.3 执行查询**：执行查询分为以下**三个步骤**

* 根据查询语法在倒排索引词典表中分别找出对应搜索词的索引，从而找到索引所链接的文档链表。比如搜索语法为”lucene AND java“表示搜索出的文档中即要包括lucene也要包括java。

![执行查询过程](.\Lucene\执行查询过程.png)

* 由于是AND，所以要对包含lucene或java词语的链表进行交集，得到文档链表应该包括每一个搜索词语。

* 获取文档中的Field域数据。

**3.4 渲染结果**：以一个友好的界面将查询结果展示给用户，用户根据搜索结果找自己想要的信息，为了帮助用户很快找到自己的结果，提供了很多展示的效果，比如搜索结果中将关键字高亮显示，百度提供的快照等。

![渲染结果](.\Lucene\渲染结果.png)

## 三、Lucene的配置和代码实现

### **1. Lucene下载**

Lucene是开发全文检索功能的工具包，从[官网](http://lucene.apache.org/) 网站下载，并解压，要求jdk1.8及以上。

### **2.使用的jar包** 

```
// Lucene包：
lucene-core-4.10.3.jar
lucene-analyzers-common-4.10.3.jar
lucene-queryparser-4.10.3.jar

// 其它：
commons-io-2.4.jar
junit-4.9.jar
```

### **3.创建索引库**

使用indexwriter对象创建索引

**3.1 实现步骤**

* 创建一个java工程，并导入jar包。

* 创建一个indexwriter对象。

  * 指定索引库的存放位置Directory对象；

  * 指定一个分析器，对文档内容进行分析。

* 创建document对象。

* 创建field对象，将field添加到document对象中。

* 使用indexwriter对象将document对象写入索引库，此过程进行索引创建。并将索引和document对象写入索引库。

* 关闭IndexWriter对象。

**3.2 Field域的属性**

* **是否分析**：**为了索引**，是否对域的内容进行分词处理，前提我们是否要对域的内容进行查询。

* **是否索引**：**为了搜索**，将Field分析后的词或整个Field值进行索引，只有索引方可搜索到。比如：商品名称、商品简介分析后进行索引，订单号、身份证号不用分析但也要索引，这些将来都要作为查询条件。

* **是否存储**：将Field值存储在文档中，存储在文档中的Field才可以从Document中获取。比如：商品名称、订单号，以后根据这些信息再去数据库查询，凡是将来要从Document中获取的Field都要存储。**是否存储的标准为是否要将内容展示给用户**，存储会加快搜索速度，但是会占用空间。

| Field类                                                      | 数据类型               | Analyzed是否分析 | Indexed是否索引 | Stored是否存储 | 说明                                                         |
| ------------------------------------------------------------ | ---------------------- | ---------------- | --------------- | -------------- | ------------------------------------------------------------ |
| StringField(FieldName, FieldValue,Store.YES))                | 字符串                 | N                | Y               | Y或N           | 这个Field用来构建一个字符串Field，但是不会进行分析，会将整个串存储在索引中，比如(订单号,姓名等)是否存储在文档中用Store.YES或Store.NO决定 |
| LongField(FieldName, FieldValue,Store.YES)                   | Long型                 | Y                | Y               | Y或N           | 这个Field用来构建一个Long数字型Field，进行分析和索引，比如(价格)是否存储在文档中用Store.YES或Store.NO决定 |
| StoredField(FieldName, FieldValue)                           | 重载方法，支持多种类型 | N                | N               | Y              | 这个Field用来构建不同类型Field不分析，不索引，但要Field存储在文档中(如图片,因为要存放图片地址) |
| TextField(FieldName, FieldValue, Store.NO)或TextField(FieldName, reader) | 字符串或流             | Y                | Y               | Y或N           | 如果是一个Reader, lucene猜测内容比较多,会采用Unstored的策略. |

* **注意：**
  * Lucene底层的算法,钱数是要分词的,因为要根据价钱进行对比。
  * 选择不存储，索引不会受到影响，但是Document中不存储。

**3.3 代码实现**

```java
//创建索引
@Test
public void createIndex() throws Exception {
    //指定索引库存放的路径
    Directory directory = FSDirectory.open(new File("D:\\temp\\0108\\index"));
    //索引库还可以存放到内存中
    //Directory directory = new RAMDirectory();
    //创建一个标准分析器
    Analyzer analyzer = new StandardAnalyzer();
    //创建indexwriterCofig对象
    //第一个参数： Lucene的版本信息，可以选择对应的lucene版本也可以使用LATEST
    //第二根参数：分析器对象
    IndexWriterConfig config = new IndexWriterConfig(Version.LATEST, analyzer);
    //创建indexwriter对象
    IndexWriter indexWriter = new IndexWriter(directory, config);
    //原始文档的路径
    File dir = new File("D:\\传智播客\\01.课程\\04.lucene\\01.参考资料\\searchsource");
    for (File f : dir.listFiles()) {
        //文件名
        String fileName = f.getName();
        //文件内容
        String fileContent = FileUtils.readFileToString(f);
        //文件路径
        String filePath = f.getPath();
        //文件的大小
        long fileSize  = FileUtils.sizeOf(f);
        //创建文件名域
        //第一个参数：域的名称
        //第二个参数：域的内容
        //第三个参数：是否存储
        Field fileNameField = new TextField("filename", fileName, Store.YES);
        //文件内容域
        Field fileContentField = new TextField("content", fileContent, Store.YES);
        //文件路径域（不分析、不索引、只存储）
        Field filePathField = new StoredField("path", filePath);
        //文件大小域
        Field fileSizeField = new LongField("size", fileSize, Store.YES);
        //除此之外还可以创建其他的有关文件的域：创建时间，文件大小...

        //创建document对象
        Document document = new Document();
        document.add(fileNameField);
        document.add(fileContentField);
        document.add(filePathField);
        document.add(fileSizeField);
        //这里通过分词器分析Document，建立索引（ 域名：词 的形式，并有指针指向原始文档），并写入索引库
        indexWriter.addDocument(document);
    }
    //关闭indexwriter
    indexWriter.close();
}
```

**3.4 使用Luke工具查看索引文件**

* 使用命令提示符进入luke目录
* 执行jar包命令，java -jar lukeall-4.10.3.jar
* 选择索引库的目录

![使用Luke工具查看索引文件](.\Lucene\使用luke工具查看索引文件.png) 

### **4.查询索引**

**4.1 实现步骤**

* 创建一个Directory对象，也就是索引库存放的位置。

* 创建一个indexReader对象，需要指定Directory对象。

* 创建一个indexsearcher对象，需要指定IndexReader对象

* 创建一个TermQuery对象，指定查询的域和查询的关键词。

* 执行查询。

* 返回查询结果。遍历查询结果并输出。

* 关闭IndexReader对象

**4.2 IndexSearcher搜索方法**

| 方法                                                | 说明                                                         |
| --------------------------------------------------- | ------------------------------------------------------------ |
| indexSearcher.search(query, n)                      | 根据Query搜索，返回评分最高的n条记录                         |
| indexSearcher.search(query, filter, n)              | 根据Query搜索，添加过滤策略，返回评分最高的n条记录           |
| indexSearcher.search(query, n, sort)                | 根据Query搜索，添加排序策略，返回评分最高的n条记录           |
| indexSearcher.search(booleanQuery, filter, n, sort) | 根据Query搜索，添加过滤策略，添加排序策略，返回评分最高的n条记录 |

**4.3 代码实现**

```java
//查询索引库
@Test
public void searchIndex() throws Exception {
	//指定索引库存放的路径
	Directory directory = FSDirectory.open(new File("D:\\temp\\0108\\index"));
	//创建indexReader对象
	IndexReader indexReader = DirectoryReader.open(directory);
	//创建indexsearcher对象
	IndexSearcher indexSearcher = new IndexSearcher(indexReader);
	//创建查询，创建查询的时候要使用和创建查询一致的分词器
    Analyzer analyzer = new StandardAnalyzer();
	QueryParse queryParse = new QueryParse("content",analyzer);
    //从filename域中查询关键字，若这里不指定域名只传“apache”，则默认搜索上面的默认搜索域“content”
    Query query = queryParse.parse("filename:apache");
	//执行查询
	//第一个参数是查询对象，第二个参数是查询结果返回的最大值
	TopDocs topDocs = indexSearcher.search(query, 10);
	//查询结果的总条数
	System.out.println("查询结果的总条数："+ topDocs.totalHits);
	//遍历查询结果
	//topDocs.scoreDocs存储了document对象的id
	for (ScoreDoc scoreDoc : topDocs.scoreDocs) {
		//scoreDoc.doc属性就是document对象的id
		//根据document的id找到document对象
		Document document = indexSearcher.doc(scoreDoc.doc);
		System.out.println(document.get("filename"));
		System.out.println(document.get("content"));
		System.out.println(document.get("path"));
		System.out.println(document.get("size"));
	}
	//关闭indexreader对象
	indexReader.close();
}
```

**4.4 TopDocs**

Lucene搜索结果可通过TopDocs遍历，TopDocs类提供了少量的属性，如下：

| 方法或属性 | 说明                   |
| ---------- | ---------------------- |
| totalHits  | 匹配搜索条件的总记录数 |
| scoreDocs  | 顶部匹配记录           |

**注意：**

* Search方法需要指定匹配记录数量n：indexSearcher.search(query, n)，可以从索引库中查询到许多条（`topDocs.totalHits`），但是最多只显示n条
  
* TopDocs.totalHits：是匹配索引库中所有记录的数量
  
* TopDocs.scoreDocs：**匹配相关度高的评分排名靠前的记录数组**，scoreDocs的长度小于等于search方法指定的参数n。

### **5.中文分词**

**5.1 分析器（Analyzer）的执行过程**

如下图是语汇单元的生成过程：

![分析器的执行过程](.\Lucene\分析器的执行过程.png) 

从一个Reader字符流开始，创建一个基于Reader的Tokenizer分词器，经过三个TokenFilter生成语汇单元Token。要看分析器的分析效果，只需要看Tokenstream中的内容就可以了。每个分析器都有一个方法tokenStream，返回一个tokenStream对象。 

**5.2 分析器的分词效果**

```java
//查看标准分析器的分词效果
public void testTokenStream() throws Exception {
	//创建一个标准分析器对象
	Analyzer analyzer = new StandardAnalyzer();
	//获得tokenStream对象
	//第一个参数：域名，可以随便给一个
	//第二个参数：要分析的文本内容
	TokenStream tokenStream = analyzer.tokenStream("test", "The Spring Framework provides a comprehensive programming and configuration model.");
	//添加一个引用，可以获得每个关键词
	CharTermAttribute charTermAttribute = tokenStream.addAttribute(CharTermAttribute.class);
	//添加一个偏移量的引用，记录了关键词的开始位置以及结束位置
	OffsetAttribute offsetAttribute = tokenStream.addAttribute(OffsetAttribute.class);
	//将指针调整到列表的头部
	tokenStream.reset();
	//遍历关键词列表，通过incrementToken方法判断列表是否结束
	while(tokenStream.incrementToken()) {
		//关键词的起始位置
		System.out.println("start->" + offsetAttribute.startOffset());
		//取关键词
		System.out.println(charTermAttribute);
		//结束位置
		System.out.println("end->" + offsetAttribute.endOffset());
	}
	tokenStream.close();
} 
```

**5.3 中文分析器**

* **Lucene自带中文分词器**：

* StandardAnalyzer：单字分词：就是按照中文一个字一个字地进行分词。如：“我爱中国”，效果：“我”、“爱”、“中”、“国”。
  
* CJKAnalyzerL：二分法分词：按两个字进行切分。如：“我是中国人”，效果：“我是”、“是中”、“中国”“国人”。
  
* SmartChineseAnalyzer：上边两个分词器无法满足需求，SmartChineseAnalyzer对中文支持较好，但扩展性差，扩展词库，禁用词库和同义词库等不好处理。
  
* **第三方中文分析器**

  * [paoding]( https://code.google.com/p/paoding/ )： 庖丁解牛最新版最多支持Lucene 3.0，且最新提交的代码在 2008-06-03，在svn中最新也是2010年提交，已经过时，不予考虑。

  * [mmseg4j](https://github.com/chenlb/mmseg4j-solr)：最新版支持Lucene 4.10，且在github中最新提交代码是2014年6月，从09年～14年一共有：18个版本，也就是一年几乎有3个大小版本，有较大的活跃度，用了mmseg算法。

  * [IK-analyzer](https://code.google.com/p/ik-analyzer/)： 最新版在上，支持Lucene 4.10从2006年12月推出1.0版开始， IKAnalyzer已经推出了4个大版本。最初，它是以开源项目Luence为应用主体的，结合词典分词和文法分析算法的中文分词组件。从3.0版本开 始，IK发展为面向Java的公用分词组件，独立于Lucene项目，同时提供了对Lucene的默认优化实现。在2012版本中，IK实现了简单的分词 歧义排除算法，标志着IK分词器从单纯的词典分词向模拟语义分词衍化。 但是也就是2012年12月后没有在更新。

  * [ansj_seg](https://github.com/NLPchina/ansj_seg)：tags仅有1.1版本，从2012年到2014年更新了大小6次，但是作者本人在2014年10月10日说明：“可能我以后没有精力来维护ansj_seg了”，现在由”nlp_china”管理。2014年11月有更新。并未说明是否支持Lucene，是一个由CRF（条件随机场）算法所做的分词算法。

  * [imdict-chinese-analyzer](https://code.google.com/p/imdict-chinese-analyzer/)：不支持Lucene 4.10 。是利用HMM（隐马尔科夫链）算法。

  * [Jcseg](https://gitee.com/lionsoul/jcseg)：支持Lucene 4.10，作者有较高的活跃度。利用mmseg算法。

**5.4 IKAnalyzer**

![IKAnalyzer分词器](.\Lucene\IKAnalyzer分词器.png) 

使用方法：

* 把jar包添加到工程中

* 使用EditPlus.exe保存为无BOM 的UTF-8 编码格式，如下图，把配置文件和扩展词典（在其中定义不可分开的专有名词的文件）和停用词词典添加到项目下的resource中


![保存为无BOM的编码格式](.\Lucene\保存为无BOM的编码格式.png) 

* **注意：**mydict.dic和ext_stopword.dic文件的格式为UTF-8，注意的是`无BOM的UTF-8 编码`。

### **6.Analyzer使用时机**

**6.1 索引时使用Analyzer**

输入关键字进行搜索，当需要让该关键字与文档域内容所包含的词进行匹配时需要对文档域内容进行分析，需要经过Analyzer分析器处理生成语汇单元（Token）。分析器分析的对象是文档中的Field域。当Field的属性tokenized（是否分词）为true时会对Field值进行分析，如下图： 

![索引时使用Analyzer](.\Lucene\索引时使用Analyzer.png) 

**对于一些Field可以不用分析：**

* 不作为查询条件的内容，比如文件路径（路径是一个整体，分析后就无意义）

* 不是匹配内容中的词而匹配Field的整体内容，比如订单号、身份证号等。

**6.2 搜索时使用Analyzer**

对搜索关键字进行分析和索引分析一样，使用Analyzer对搜索关键字进行分析、分词处理，使用分析后每个词语进行搜索。比如：搜索关键字`spring web`，经过分析器进行分词，得出`spring web`拿词去索引词典表查找 ，找到索引链接到Document，解析Document内容。对于匹配整体Field域的查询可以在搜索时不分析，比如根据订单号、身份证号查询等。

* **注意：**搜索使用的分析器要和索引使用的分析器一致。 

### **7.索引库的维护**

索引库的维护都是根据Term词元对指定域名包含关键字的域中进行更改。

**7.1 索引库的添加**

* **步骤：**

  * 向索引库中添加document对象。

  * 先创建一个indexwriter对象

  * 创建一个document对象

  * 把document对象写入索引库

  * 关闭indexwriter。

* **代码实现**

```java
//添加索引
@Test
public void addDocument() throws Exception {
	//索引库存放路径
	Directory directory = FSDirectory.open(new File("D:\\temp\\0108\\index"));
	
	IndexWriterConfig config = new IndexWriterConfig(Version.LATEST, new IKAnalyzer());
	//创建一个indexwriter对象
	IndexWriter indexWriter = new IndexWriter(directory, config);
	//创建一个Document对象
	Document document = new Document();
	//向document对象中添加域。
	//不同的document可以有不同的域，同一个document可以有相同的域，相同的域可以同时存在，不合并，是独立的两个域，存放在这个document中，doucument存放会默认生成一个id值排序，在查询的时候也会在这个域中查询并返回两个域中的结果。
	document.add(new TextField("filename", "新添加的文档", Store.YES));
	document.add(new TextField("content", "新添加的文档的内容", Store.NO));
	document.add(new TextField("content", "新添加的文档的内容第二个content", Store.YES));
	document.add(new TextField("content1", "新添加的文档的内容要能看到", Store.YES));
	//创建索引，将索引指向文档，并将索引和文档添加到索引库，
	indexWriter.addDocument(document);
	//关闭indexwriter
	indexWriter.close();	
}
```

**7.2 索引库删除**

* **删除全部：**将索引目录的索引信息全部删除，直接彻底删除，无法恢复。

```java
//删除全部索引
@Test
public void deleteAllIndex() throws Exception {
	IndexWriter indexWriter = getIndexWriter();
	//删除全部索引
	indexWriter.deleteAll();
	//关闭indexwriter
	indexWriter.close();
}
```

* **指定查询条件删除**

```java
//根据查询条件删除索引
@Test
public void deleteIndexByQuery() throws Exception {
	IndexWriter indexWriter = getIndexWriter();
	//创建一个查询条件
	Query query = new TermQuery(new Term("filename", "apache"));
	//根据查询条件删除
	indexWriter.deleteDocuments(query);
	//关闭indexwriter
	indexWriter.close();
}
```

**7.3 索引库的修改**

修改就是按照传入的Term的域名进行搜索,如果找到结果那么删除,将修改的内容重新生成一个Document对象，如果没有搜索到结果,那么将修改的内容直接添加一个新的Document对象。

```java
//修改索引库
@Test
public void updateIndex() throws Exception {
	IndexWriter indexWriter = getIndexWriter();
	//创建一个Document对象
	Document document = new Document();
	//向document对象中添加域。
	//不同的document可以有不同的域，同一个document可以有相同的域。
	document.add(new TextField("filename", "要更新的文档", Store.YES));
	document.add(new TextField("content", "2013年11月18日 - Lucene 简介 Lucene 是一个基于 Java 的全文信息检索工具包,它不是一个完整的搜索应用程序,而是为你的应用程序提供索引和搜索功能。", Store.YES));
	indexWriter.updateDocument(new Term("content", "java"), document);
	//关闭indexWriter
	indexWriter.close();
}
```

**7.4 索引库的查询（重点）**

对要搜索的信息创建Query查询对象，Lucene会根据Query查询对象生成最终的查询语法，类似关系数据库Sql语法一样Lucene也有自己的查询语法。

可通过主要的两种方法创建查询对象（还有一些其他的）：

* **使用Lucene提供Query子类：**Query是一个抽象类，lucene提供了很多查询对象，比如TermQuery项精确查询，NumericRangeQuery数字范围查询等。
* **使用QueryParse解析查询表达式：**QueryParse会将用户输入的查询表达式解析成Query对象实例。

[lucene当中的各种query](https://blog.csdn.net/wwty1314/article/details/83638612)

**7.4.1 使用Query的子类查询**

```java
//公共方法

//执行查询
public static void printResult(Query query, IndexSearcher indexSearcher){
	//第一个参数是查询对象，第二个参数是查询结果返回的最大值
	TopDocs topDocs = indexSearcher.search(query, 10);
	//查询结果的总条数
	System.out.println("查询结果的总条数："+ topDocs.totalHits);
	//遍历查询结果
	//topDocs.scoreDocs存储了document对象的id
	for (ScoreDoc scoreDoc : topDocs.scoreDocs) {
		//scoreDoc.doc属性就是document对象的id
		//根据document的id找到document对象
		Document document = indexSearcher.doc(scoreDoc.doc);
		System.out.println(document.get("filename"));
		System.out.println(document.get("content"));
		System.out.println(document.get("path"));
		System.out.println(document.get("size"));
	}
    //关闭indexreader
    indexSearcher.getIndexReader().close();
}

//获取搜索对象
public static IndexSearcher getIndexSearcher(){
    //指定索引库存放的路径
	Directory directory = FSDirectory.open(new File("D:\\temp\\0108\\index"));
	//创建indexReader对象
	IndexReader indexReader = DirectoryReader.open(directory);
	//创建indexsearcher对象
	IndexSearcher indexSearcher = new IndexSearcher(indexReader);
    
    return indexSearcher;
}
```

* **TermQuery：**通过项查询，TermQuery不使用分析器所以建议匹配不分词的Field域查询，比如订单号、分类ID号等。指定要查询的域和要查询的关键词。

```java
//使用Termquery查询
@Test
public void testTermQuery() throws Exception {
    IndexSearcher indexSearcher = this.getIndexSearcher();
    //创建查询对象
    Query query = new TermQuery(new Term("content", "lucene"));
    //执行查询
	printResult(query, indexSearcher);
}
```

* **NumericRangeQuery：**可以根据数值范围查询。

```java
//数值范围查询
@Test
public void testNumericRangeQuery() throws Exception {
    IndexSearcher indexSearcher = this.getIndexSearcher();
    //创建查询
    //参数(域名,最小值,最大值,是否包含最小值,是否包含最大值)
    Query query = NumericRangeQuery.newLongRange("size", 1l, 1000l, true, true);
    //执行查询
    printResult(query, indexSearcher);
}
```

* **BooleanQuery：**可以组合查询条件。

  * **注意：**单独使用must_not  或者 独自使用must_not没有任何意义。

```java
//组合条件查询
@Test
public void testBooleanQuery() throws Exception {
    IndexSearcher indexSearcher = this.getIndexSearcher();
    //创建一个布尔查询对象
    BooleanQuery booleanQuery = new BooleanQuery();
    //创建第一个查询条件
    Query query1 = new TermQuery(new Term("filename", "apache"));
    Query query2 = new TermQuery(new Term("content", "apache"));
    //组合查询条件
    booleanQuery.add(query1, Occur.MUST);
    booleanQuery.add(query2, Occur.MUST);
    //执行查询
    printResult(booleanQuery, indexSearcher);
}
```

| 组合查询条件属性 | 说明                                 | 符号           |
| ---------------- | ------------------------------------ | -------------- |
| Occur.MUST       | 必须满足此条件，相当于and            | +              |
| Occur.SHOULD     | 应该满足，但是不满足也可以，相当于or | 空（不写符号） |
| Occur.MUST_NOT   | 必须不满足。相当于not                | -              |

* **MatchAllDocsQuery：**使用MatchAllDocsQuery查询索引目录中的所有文档

```java
@Test
public void testMatchAllDocsQuery() throws Exception {
    IndexSearcher indexSearcher = this.getIndexSearcher();
    //创建查询条件
    Query query = new MatchAllDocsQuery();
    //执行查询
    printResult(query, indexSearcher);
}
```

**7.4.2 使用QueryParser查询**

导入`lucence-queryparser-4.10.3.jar`，通过QueryParser也可以创建Query，QueryParser提供一个Parse方法，此方法可以直接根据查询语法来查询。Query对象执行的查询语法可通过`System.out.println(query)`输出。需要使用到分析器，建议创建索引时使用的分析器和查询索引时使用的分析器要一致。

* **QueryParser**

  * **注意：**

    * 范围查询在lucene中不支持数值类型，支持字符串类型。在solr中支持数值类型。

    * 只能从文本中进行搜索

```java
@Test
public void testQueryParser() throws Exception {
    IndexSearcher indexSearcher = this.getIndexSearcher();
    //创建queryparser对象
    //第一个参数默认搜索的域
    //第二个参数就是分析器对象
    QueryParser queryParser = new QueryParser("content", new IKAnalyzer());
    //查询条件
    Query query = queryParser.parse("content:Lucene是java开发的");
    //执行查询
    printResult(query, indexSearcher);
}
```

| 查询方式     | 查询语法                                                     |
| ------------ | ------------------------------------------------------------ |
| 关键词查询   | `域名:搜索的关键字`，例如：content:java                      |
| 范围查询     | `域名:[最小值 TO 最大值]`，例如：size:[1 TO 1000]            |
| 组合条件查询 | （1）`+条件1 +条件2`：A and B，例如：+filename:apache +content:apache 或者 filename:apache AND content:apache； |
|              | （2）`+条件1 条件2`：必须满足条件1，应该满足条件3，例如：+filename:apache content:apache； |
|              | （3）`条件1 条件2`：A or B，例如：filename:apache content:apache 或者 filename:apache OR content:apache ； |
|              | （4）`-条件1 条件2`：必须不满足条件1，应该满足条件2，例如：-filename:apache content:apache 或者 NOT filename:apache content:apache。 |
| 全部查询     | `*:*`，查询全部                                              |

* **MulitFieldQueryParser：**可以指定多个默认搜索域

```java
@Test
public void testMultiFiledQueryParser() throws Exception {
    IndexSearcher indexSearcher = getIndexSearcher();
    //可以指定默认搜索的域是多个
    String[] fields = {"filename", "content"};
    //创建一个MulitFiledQueryParser对象
    MultiFieldQueryParser queryParser = new MultiFieldQueryParser(fields, new IKAnalyzer());
    Query query = queryParser.parse("java and apache");
    System.out.println(query);//(filename:java content:java) (filename:and content:and) (filename:apache content:apache)
    //执行查询
    printResult(query, indexSearcher);
}
```

## 参考：

[随笔分类 - 搜索引擎](https://www.cnblogs.com/leeSmall/category/1210814.html)

