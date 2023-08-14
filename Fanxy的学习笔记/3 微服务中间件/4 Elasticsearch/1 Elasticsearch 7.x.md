# Elasticsearch 7.x

# 第1章 Elasticsearch 概述

## **1.1 Elasticsearch** **是什么**

![1](./1 Elasticsearch 7.x.assets/1.png)

The Elastic Stack, 包括 Elasticsearch、Kibana、Beats 和 Logstash（也称为 ELK Stack）。

能够安全可靠地获取任何来源、任何格式的数据，然后实时地对数据进行搜索、分析和可视化。Elaticsearch，简称为 ES，ES 是一个**开源的高扩展的分布式全文搜索引擎，**是整个 Elastic Stack 技术栈的核心。它可以近乎实时的存储、检索数据；本身扩展性很好，可以扩展到上百台服务器，处理 PB 级别的数据。

## 1.2 全文搜索引擎

Google，百度类的网站搜索，它们都是根据网页中的关键字生成索引，我们在搜索的时候输入关键字，它们会将该关键字即索引匹配到的所有网页返回；还有常见的项目中应用日志的搜索等等。对于这些非结构化的数据文本，关系型数据库搜索不是能很好的支持。

一般传统数据库，全文检索都实现的很鸡肋，因为一般也没人用数据库存文本字段。进行全文检索需要扫描整个表，如果数据量大的话即使对 SQL 的语法优化，也收效甚微。建立了索引，但是维护起来也很麻烦，对于 insert 和 update 操作都会重新构建索引。

基于以上原因可以分析得出，在一些生产环境中，使用常规的搜索方式，性能是非常差的：

- 搜索的数据对象是大量的非结构化的文本数据。

- 文件记录量达到数十万或数百万个甚至更多。

- 支持大量基于交互式文本的查询。

- 需求非常灵活的全文搜索查询。

- 对高度相关的搜索结果的有特殊需求，但是没有可用的关系数据库可以满足。

- 对不同记录类型、非文本数据操作或安全事务处理的需求相对较少的情况。

为了解决结构化数据搜索和非结构化数据搜索性能问题，我们就需要专业，健壮，强大的全文搜索引擎

这里说到的全文搜索引擎指的是目前广泛应用的主流搜索引擎。它的工作原理是计算机索引程序通过扫描文章中的每一个词，对每一个词建立一个索引，指明该词在文章中出现的次数和位置，当用户查询时，检索程序就根据事先建立的索引进行查找，并将查找的结果反馈给用户的检索方式。这个过程类似于通过字典中的检索字表查字的过程。

## 1.3 Elasticsearch And Solr

Lucene 是 Apache 软件基金会 Jakarta 项目组的一个子项目，提供了一个简单却强大的应用程式接口，能够做全文索引和搜寻。在 Java 开发环境里 Lucene 是一个成熟的免费开源工具。就其本身而言，Lucene 是当前以及最近几年最受欢迎的免费 Java 信息检索程序库。

但 Lucene 只是一个提供全文搜索功能类库的核心工具包，而真正使用它还需要一个完善的服务框架搭建起来进行应用。

目前市面上流行的搜索引擎软件，主流的就两款：**Elasticsearch** 和 **Solr**,这两款都是基于 Lucene 搭建的，可以独立部署启动的搜索引擎服务软件。由于内核相同，所以两者除了服务器安装、部署、管理、集群以外，对于数据的操作 修改、添加、保存、查询等等都十分类似。

在使用过程中，一般都会将 Elasticsearch 和 Solr 这两个软件对比，然后进行选型。这两个搜索引擎都是流行的，先进的的开源搜索引擎。它们都是围绕核心底层搜索库 - Lucene构建的 - 但它们又是不同的。像所有东西一样，每个都有其优点和缺点：

| 特征              | Solr/SolrCloud                                               | Elasticsearch                                                |
| ----------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 社区和开发者      | Apache 软件基金和社区支持                                    | 单一商业实体及其员工                                         |
| 节点发现          | Apache Zookeeper，在大量项目中成熟且经过实战测试             | Zen内置于Elasticsearch本身，需要专用的主节点才能进行分裂脑保护 |
| 碎片放置          | 本质上是静态，需要手动工作来迁移分片，从Solr 7开始 Autoscaling API允i许一些动态操作 | 动态。可以根据洋集状态按活移动分片                           |
| 高速缓存          | 全局，每个段更改无效                                         | 每段，更适合动态更改数据                                     |
| 分析引擎性能      | 非常适合精确计算的静态数据                                   | 结果的准确性取决于数据放置                                   |
| 全文搜索功能      | 基于Lucene的语言分析，多建议，拼写检查，丰富的高亮显示支持   | 基于Lucene的语言分析，单一建议API实现，高亮显示，重新计算    |
| DevOps支持        | 尚未完全,但即将到来                                          | 非常好的API                                                  |
| 非平面数据处理    | 嵌套文档和父-子支持                                          | 嵌套和对象类型的自然支持允许几乎无限的嵌套和父-子支持        |
| 查询DSL           | JSON(有限)，XML(有限)或或URL参数                             | JSON                                                         |
| 索引/收集领导控制 | 领导者安置控制和领导者王新平衡甚至可以节点上的负载           | 不可能                                                       |
| 机器学习          | 内置–在流聚合之上，专注于逻辑回归和学习排名贡献模块          | 商业功能，专注于异常和异常值以及时间序列数据                 |

## 1.4 Elasticsearch Or Solr

Elasticsearch 和 Solr 都是开源搜索引擎，那么我们在使用时该如何选择呢？

- Google 搜索趋势结果表明，与 Solr 相比，Elasticsearch 具有很大的吸引力，但这并不意味着 Apache Solr 已经死亡。虽然有些人可能不这么认为，但 Solr 仍然是最受欢迎的搜索引擎之一，拥有强大的社区和开源支持。

- 与 Solr 相比，Elasticsearch 易于安装且非常轻巧。此外，你可以在几分钟内安装并运行Elasticsearch。但是，如果 Elasticsearch 管理不当，这种易于部署和使用可能会成为一个问题。基于 JSON 的配置很简单，但如果要为文件中的每个配置指定注释，那么它不适合您。总的来说，如果你的应用使用的是 JSON，那么 Elasticsearch 是一个更好的选择。否则，请使用 Solr，因为它的 schema.xml 和 solrconfig.xml 都有很好的文档记录。

- Solr 拥有更大，更成熟的用户，开发者和贡献者社区。ES 虽拥有的规模较小但活跃的用户社区以及不断增长的贡献者社区。Solr 贡献者和提交者来自许多不同的组织，而 Elasticsearch 提交者来自单个公司。

- Solr 更成熟，但 ES 增长迅速，更稳定。

- Solr 是一个非常有据可查的产品，具有清晰的示例和 API 用例场景。 Elasticsearch 的文档组织良好，但它缺乏好的示例和清晰的配置说明。

**那么，到底是** **Solr** **还是** **Elasticsearch？**

有时很难找到明确的答案。无论您选择 Solr 还是 Elasticsearch，首先需要了解正确的用例和未来需求。总结他们的每个属性。

- 由于易于使用，Elasticsearch 在新开发者中更受欢迎。一个下载和一个命令就可以启动一切。

- 如果除了搜索文本之外还需要它来处理分析查询，Elasticsearch 是更好的选择。

- 如果需要分布式索引，则需要选择 Elasticsearch。对于需要良好可伸缩性和以及性能分布式环境，Elasticsearch 是更好的选择。

- Elasticsearch 在开源日志管理用例中占据主导地位，许多组织在 Elasticsearch 中索引它们的日志以使其可搜索。

- 如果你喜欢监控和指标，那么请使用 Elasticsearch，因为相对于 Solr，Elasticsearch 暴露了更多的关键指标。

## 1.5 Elasticsearch 应用案例

- **GitHub**: 2013 年初，抛弃了 Solr，采取 Elasticsearch 来做 PB 级的搜索。“GitHub 使用 Elasticsearch 搜索 20TB 的数据，包括 13 亿文件和 1300 亿行代码”。

- **维基百科**：启动以 Elasticsearch 为基础的核心搜索架构

- **SoundCloud**：“SoundCloud 使用 Elasticsearch 为 1.8 亿用户提供即时而精准的音乐搜索服务”。

- **百度**：目前广泛使用 Elasticsearch 作为文本数据分析，采集百度所有服务器上的各类指标数据及用户自定义数据，通过对各种数据进行多维分析展示，辅助定位分析实例异常或业务层面异常。目前覆盖百度内部 20 多个业务线（包括云分析、网盟、预测、文库、直达号、钱包、风控等），单集群最大 100 台机器，200 个 ES 节点，每天导入 30TB+数据。

- **新浪**：使用 Elasticsearch 分析处理 32 亿条实时日志。

- **阿里**：使用 Elasticsearch 构建日志采集和分析体系。

- **Stack Overflow**：解决 Bug 问题的网站，全英文，编程人员交流的网站。

# **第2章** Elasticsearch **入门**

## **2.1 Elasticsearch** **安装**

### **2.1.1** **下载软件**

Elasticsearch 的官方地址：https://www.elastic.co/cn/

Elasticsearch 最新的版本是 7.11.2（截止 2021.3.10），我们选择 7.8.0 版本（最新版本半年前的版本）

下载地址：https://www.elastic.co/cn/downloads/past-releases#elasticsearch

Elasticsearch 分为 Linux 和 Windows 版本，基于我们主要学习的是 Elasticsearch 的 Java客户端的使用，所以课程中使用的是安装较为简便的 Windows 版本。

### **2.1.2** **安装软件**

Windows 版的 Elasticsearch 的安装很简单，解压即安装完毕，解压后的 Elasticsearch 的

目录结构如下

![2.png](./1 Elasticsearch 7.x.assets/2.png)

| 目录    | 含义           |
| ------- | -------------- |
| bin     | 可执行脚本目录 |
| config  | 配置目录       |
| jdk     | 内置jdk目录    |
| lib     | 类库           |
| logs    | 日志目录       |
| modules | 模块目录       |
| plugins | 插件目录       |

解压后，进入 bin 文件目录，点击 elasticsearch.bat 文件启动 ES 服务

**注意：9300 端口为** **Elasticsearch** **集群间组件的通信端口，9200 端口为浏览器访问的** **http协议** **RESTful** **端口。**

打开浏览器（推荐使用谷歌浏览器），输入地址：http://localhost:9200，测试结果

![3.png](./1 Elasticsearch 7.x.assets/3.png)

### 2.1.3 问题解决

- Elasticsearch 是使用 java 开发的，且 7.8 版本的 ES 需要 JDK 版本 1.8 以上，默认安装包带有 jdk 环境，如果系统配置 JAVA_HOME，那么使用系统默认的 JDK，如果没有配置使用自带的 JDK，一般建议使用系统配置的 JDK。

- 双击启动窗口闪退，通过路径访问追踪错误，如果是“空间不足”，请修改config/jvm.options 配置文件

```sh
# 设置 JVM 初始内存为 1G。此值可以设置与-Xmx 相同，以避免每次垃圾回收完成后 JVM 重新分配内存

# Xms represents the initial size of total heap space

# 设置 JVM 最大可用内存为 1G

# Xmx represents the maximum size of total heap space

-Xms1g
-Xmx1g
```

## **2.2 Elasticsearch** **基本操作**

### **2.2.1 RESTful**

REST 指的是一组架构约束条件和原则。满足这些约束条件和原则的应用程序或设计就是 RESTful。Web 应用程序最重要的 REST 原则是，客户端和服务器之间的交互在请求之间是无状态的。从客户端到服务器的每个请求都必须包含理解请求所必需的信息。如果服务器在请求之间的任何时间点重启，客户端不会得到通知。此外，无状态请求可以由任何可用服务器回答，这十分适合云计算之类的环境。客户端可以缓存数据以改进性能。

在服务器端，应用程序状态和功能可以分为各种资源。资源是一个有趣的概念实体，它向客户端公开。资源的例子有：应用程序对象、数据库记录、算法等等。每个资源都使用 URI (Universal Resource Identifier) 得到一个唯一的地址。所有资源都共享统一的接口，以便在客户端和服务器之间传输状态。使用的是标准的 HTTP 方法，比如 GET、PUT、POST 和 DELETE。

在 REST 样式的 Web 服务中，每个资源都有一个地址。资源本身都是方法调用的目标，方法列表对所有资源都是一样的。这些方法都是标准方法，包括 HTTP GET、POST、PUT、DELETE，还可能包括 HEAD 和 OPTIONS。简单的理解就是，如果想要访问互联网上的资源，就必须向资源所在的服务器发出请求，请求体中必须包含资源的网络路径，以及对资源进行的操作(增删改查)

### 2.2.2 客户端安装

如果直接通过浏览器向 Elasticsearch 服务器发请求，那么需要在发送的请求中包含HTTP 标准的方法，而 HTTP 的大部分特性且仅支持 GET 和 POST 方法。所以为了能方便地进行客户端的访问，可以使用 Postman 软件

Postman 是一款强大的网页调试工具，提供功能强大的 Web API 和 HTTP 请求调试。软件功能强大，界面简洁明晰、操作方便快捷，设计得很人性化。Postman 中文版能够发送任何类型的 HTTP 请求 (GET, HEAD, POST, PUT..)，不仅能够表单提交，且可以附带任意类型请求体。

Postman 官网：https://www.getpostman.com

Postman 下载：https://www.getpostman.com/apps

### 2.2.3 数据格式

Elasticsearch 是面向文档型数据库，一条数据在这里就是一个文档。为了方便大家理解，我们将 Elasticsearch 里存储文档数据和关系型数据库 MySQL 存储数据的概念进行一个类比

![4.png](./1 Elasticsearch 7.x.assets/4.png)

ES 里的 Index 可以看做一个库，而 Types 相当于表，Documents 则相当于表的行。这里 Types 的概念已经被逐渐弱化，Elasticsearch 6.X 中，一个 index 下已经只能包含一个type，Elasticsearch 7.X 中, Type 的概念已经被删除了。

6 用 JSON 作为文档序列化的格式，比如一条用户信息：

```json
{
 "name" : "John",
 "sex" : "Male",
 "age" : 25,
 "birthDate": "1990/05/01",
 "about" : "I love to go rock climbing",
 "interests": [ "sports", "music" ]
}
```



### 2.2.4 HTTP 操作

#### 2.2.4.1 索引操作

##### **1)** **创建索引**

对比关系型数据库，创建索引就等同于创建数据库

在 Postman 中，向 ES 服务器发 **PUT** 请求 ：http://127.0.0.1:9200/shopping

![5.png](./1 Elasticsearch 7.x.assets/5.png)

```json
{

 "acknowledged"【响应结果】: true, # true 操作成功

 "shards_acknowledged"【分片结果】: true, # 分片操作成功

 "index"【索引名称】: "shopping"

}
```

**注意：创建索引库的分片数默认 1 片，在 7.0.0 之前的 Elasticsearch 版本中，默认 5 片如果重复添加索引，会返回错误信息**

![6.png](./1 Elasticsearch 7.x.assets/6.png)

##### **2)** **查看所有索引**

在 Postman 中，向 ES 服务器发 **GET** 请求 

```sh
http://127.0.0.1:9200/_cat/indices?v
```

这里请求路径中的_cat 表示查看的意思，indices 表示索引，所以整体含义就是查看当前 ES 服务器中的所有索引，就好像 MySQL 中的 show tables 的感觉，服务器响应结果如下

![7.png](./1 Elasticsearch 7.x.assets/7.png)

![8.png](./1 Elasticsearch 7.x.assets/8.png)

##### **3)** **查看单个索引**

在 Postman 中，向 ES 服务器发 **GET** 请求 ：

```sh
http://127.0.0.1:9200/shopping
```

查看索引向 ES 服务器发送的请求路径和创建索引是一致的。但是 HTTP 方法不一致。这里可以体会一下 RESTful 的意义

![9.png](./1 Elasticsearch 7.x.assets/9.png)

```json
{
	"shopping"【索引名】: { 
         "aliases"【别名】: {},
         "mappings"【映射】: {},
         "settings"【设置】: {
             "index"【设置 - 索引】: {
                 "creation_date"【设置 - 索引 - 创建时间】: "1614265373911",
                 "number_of_shards"【设置 - 索引 - 主分片数量】: "1",
                 "number_of_replicas"【设置 - 索引 - 副分片数量】: "1",
                 "uuid"【设置 - 索引 - 唯一标识】: "eI5wemRERTumxGCc1bAk2A",
                 "version"【设置 - 索引 - 版本】: {
                     "created": "7080099"
                 },
                 "provided_name"【设置 - 索引 - 名称】: "shopping"
     		}
     	}
     }
}
```

##### **4)** **删除索引**

在 Postman 中，向 ES 服务器发 **DELETE** 请求 ：

```sh
http://127.0.0.1:9200/shopping
```

重新访问索引时，服务器返回响应：**索引不存在**

#### 2.2.4.2 文档操作

##### **1)** **创建文档**

索引已经创建好了，接下来我们来创建文档，并添加数据。这里的文档可以类比为关系型数据库中的表数据，添加的数据格式为 JSON 格式

在 Postman 中，向 ES 服务器发 **POST** 请求 ：

```sh
http://127.0.0.1:9200/shopping/_doc
```

请求体内容为：

```json
{
    "title":"小米手机",
    "category":"小米",
    "images":"http://www.gulixueyuan.com/xm.jpg",
    "price":3999.00
}
```

此处发送请求的方式必须为 **POST**，不能是 **PUT**，否则会发生错误

<img src="./1 Elasticsearch 7.x.assets/10.png" alt="10.png" style="zoom:150%;" />

服务器响应结果如下：

![11.png](./1 Elasticsearch 7.x.assets/11.png)

```json
{
    "_index"【索引】: "shopping",
    "_type"【类型-文档】: "_doc",
    "_id"【唯一标识】: "Xhsa2ncBlvF_7lxyCE9G", #可以类比为 MySQL 中的主键，随机生成
    "_version"【版本】: 1,
    "result"【结果】: "created", #这里的 create 表示创建成功
    "_shards"【分片】: {
    "total"【分片 - 总数】: 2,
    "successful"【分片 - 成功】: 1,
    "failed"【分片 - 失败】: 0
    },
    "_seq_no": 0,
    "_primary_term": 1
}
```

上面的数据创建后，由于没有指定数据唯一性标识（ID），默认情况下，ES 服务器会随机生成一个。

如果想要自定义唯一性标识，需要在创建时指定

```sh
http://127.0.0.1:9200/shopping/_doc/1001
```

**<font color='bb000'>此处需要注意：如果增加数据时明确数据主键，那么请求方式也可以为 PUT</font>**

##### **2)** **查看文档**

查看对应文档的所有数据

```sh
http://127.0.0.1:9200/shopping/_doc/_search
```

![12.png](./1 Elasticsearch 7.x.assets/12.png)

查看文档时，需要指明文档的唯一性标识，类似于 MySQL 中数据的主键查询

在 Postman 中，向 ES 服务器发 **GET** 请求 ：

```sh
http://127.0.0.1:9200/shopping/_doc/1
```

查询成功后，服务器响应结果：

```json
{
    "_index"【索引】: "shopping",
    "_type"【文档类型】: "_doc",
    "_id": "1",
    "_version": 2,
    "_seq_no": 2,
    "_primary_term": 2,
    "found"【查询结果】: true, # true 表示查找到，false 表示未查找到
    "_source"【文档源信息】: {
        "title": "华为手机",
        "category": "华为",
        "images": "http://www.gulixueyuan.com/hw.jpg",
        "price": 4999.00
    }
}
```

##### **3)** **修改文档**

和新增文档一样，输入相同的 URL 地址请求，如果请求体变化，会将原有的数据内容覆盖

在 Postman 中，向 ES 服务器发 **POST** 请求 ：

```sh
http://127.0.0.1:9200/shopping/_doc/1001
```

请求体内容为:

```sh
{
    "title":"华为手机",
    "category":"华为",
    "images":"http://www.gulixueyuan.com/hw.jpg",
    "price":4999.00
}
```

修改成功后，服务器响应结果：

```json
{
     "_index": "shopping",
     "_type": "_doc",
     "_id": "1",
     "_version"【版本】: 2,
     "result"【结果】: "updated", # updated 表示数据被更新
     "_shards": {
         "total": 2,
         "successful": 1,
         "failed": 0
     },
     "_seq_no": 2,
     "_primary_term": 2
}
```

##### **4)** **修改字段**

修改数据时，也可以只修改某一给条数据的局部信息

在 Postman 中，向 ES 服务器发 **POST** 请求 ：

**局部修改每次结果的ID是随机生成的，所以不是幂等性的**

```sh
http://127.0.0.1:9200/shopping/_update/1001
```

请求体内容为：

```json
{ 
    "doc": {
    	"price":3000.00
    } 
}
```

修改成功后，服务器响应结果为 “updated”

根据唯一性标识，查询文档数据，文档数据已经更新

```sh
http://127.0.0.1:9200/shopping/_doc/1001
```

##### **5)** **删除文档**

**<font color='bb000'>删除一个文档不会立即从磁盘上移除，它只是被标记成已删除（逻辑删除）。</font>**

在 Postman 中，向 ES 服务器发 **DELETE** 请求 ：

```sh
http://127.0.0.1:9200/shopping/_doc/1001
```

删除成功，服务器响应结果：

```json
{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "1",
    "_version"【版本】: 4, #对数据的操作，都会更新版本
    "result"【结果】: "deleted", # deleted 表示数据被标记为删除
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 4,
    "_primary_term": 2
}
```

删除后再查询当前文档信息 **GET**

```sh
http://127.0.0.1:9200/shopping/_doc/1001
```

如果删除一个并不存在的文档

```json
{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "1",
    "_version": 1,
    "result"【结果】: "not_found", # not_found 表示未查找到
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 5,
    "_primary_term": 2
}
```

##### **6)** **条件删除文档**

一般删除数据都是根据文档的唯一性标识进行删除，实际操作时，也可以根据条件对多条数据进行删除

首先分别增加多条数据:

```json
{
    "title":"小米手机",
    "category":"小米",
    "images":"http://www.gulixueyuan.com/xm.jpg",
    "price":4000.00
}
{
    "title":"华为手机",
    "category":"华为",
    "images":"http://www.gulixueyuan.com/hw.jpg",
    "price":4000.00
}
```

向 ES 服务器发 **<font color='bb000'>POST</font>** 请求 ：

```sh
http://127.0.0.1:9200/shopping/_delete_by_query
```

请求体内容为：

```json
{
 "query":{
     "match":{
         "price":4000.00
         }
     }
}
```

删除成功后，服务器响应结果：

```json
{
    "took"【耗时】: 175,
    "timed_out"【是否超时】: false,
    "total"【总数】: 2,
    "deleted"【删除数量】: 2,
    "batches": 1,
    "version_conflicts": 0,
    "noops": 0,
    "retries": {
        "bulk": 0,
        "search": 0
    },
    "throttled_millis": 0,
    "requests_per_second": -1.0,
    "throttled_until_millis": 0,
    "failures": []
}
```

#### 2.2.4.3 映射操作

有了索引库，等于有了数据库中的 database。

接下来就需要建索引库(index)中的映射了，类似于数据库(database)中的表结构(table)。

创建数据库表需要设置字段名称，类型，长度，约束等；索引库也一样，需要知道这个类型下有哪些字段，每个字段有哪些约束信息，这就叫做映射(mapping)。

##### **1)** **创建映射**

在 Postman 中，向 ES 服务器发 **PUT** 请求 ：

```sh
http://127.0.0.1:9200/student/_mapping
```

请求体内容为：

```json
{
        "properties": {
            "name":{
                "type": "text",
                "index": true
            },
            "sex":{
                "type": "keyword",
                "index": true
            },
            "age":{
                "type": "long",
                "index": false
            }
    }
}
```



**服务器响应结果如下：**

**映射数据说明：**

- **字段名：任意填写，下面指定许多属性，例如：title、subtitle、images、price**

- **type：类型，Elasticsearch 中支持的数据类型非常丰富，说几个关键的:**
  - **String 类型，又分两种：**
    - **text：可分词**
    - **keyword：不可分词，数据会作为完整字段进行匹配**
  - **Numerical：数值类型，分两类**
    - **基本数据类型：long、integer、short、byte、double、float、half_float**
    - **浮点数的高精度类型：scaled_float**
  - **Date：日期类型**
  - **Array：数组类型**
  - **Object：对象**

- **index：是否索引，默认为 true，也就是说你不进行任何配置，所有字段都会被索引。**
  - **true：字段会被索引，则可以用来进行搜索**
  - **false：字段不会被索引，不能用来搜索**

- **store：是否将数据进行独立存储，默认为 false**
  - **原始的文本会存储在`_source` 里面，默认情况下其他提取出来的字段都不是独立存储的，是从`_source` 里面提取出来的。当然你也可以独立的存储某个字段，只要设置"store": true 即可，获取独立存储的字段要比从`_source` 中解析快得多，但是也会占用更多的空间，所以要根据实际业务需求来设置。**

- **analyzer：分词器，这里的 ik_max_word 即使用 ik 分词器,后面会有专门的章节学习**

##### **2)** **查看映射**

在 Postman 中，向 ES 服务器发 **GET** 请求 ：

```sh
http://127.0.0.1:9200/student/_mapping
```

服务器响应结果如下：

![13.png](./1 Elasticsearch 7.x.assets/13.png)

##### **3)** **索引映射关联**

在 Postman 中，向 ES 服务器发 **PUT** 请求 ：

```sh
http://127.0.0.1:9200/student1
```

```json
{
    "settings": {},
    "mappings": {
        "properties": {
            "name":{
            "type": "text",
            "index": true
    		},
        "sex":{
            "type": "text",
            "index": false
    		},
        "age":{
            "type": "long",
            "index": false
    		}
    	}
    }
}
```

服务器响应结果如下：

![14.png](./1 Elasticsearch 7.x.assets/14.png)

#### 2.2.4.4 高级查询

Elasticsearch 提供了基于 JSON 提供完整的查询 DSL 来定义查询

##### **1)** **查询所有文档**

在 Postman 中，向 ES 服务器发 **GET** 请求 ：

```sh
http://127.0.0.1:9200/student/_search
```

```json
{
    "query": {
    "match_all": {}
    }
}

# "query"：这里的 query 代表一个查询对象，里面可以有不同的查询属性

# "match_all"：查询类型，例如：match_all(代表查询所有) match, term, range 等等

# {查询条件}：查询条件会根据类型的不同，写法也有差异
```

**服务器响应结果如下：**

```json
{
    "took【查询花费时间，单位毫秒】" : 1116,
    "timed_out【是否超时】" : false,
    "_shards【分片信息】" : {
        "total【总数】" : 1,
        "successful【成功】" : 1,
        "skipped【忽略】" : 0,
        "failed【失败】" : 0
    },
    "hits【搜索命中结果】" : {
    	"total"【搜索条件匹配的文档总数】: {
            "value"【总命中计数的值】: 3,
            "relation"【计数规则】: "eq" # eq 表示计数准确， gte 表示计数不准确
        },
        "max_score【匹配度分值】" : 1.0,
        "hits【命中结果集合】" : [
        		。。。
    		}
    	]
    }
}
```

##### **2)** **匹配查询**

match 匹配类型查询，会把查询条件进行分词，然后进行查询，多个词条之间是 or 的关系

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

**query String**

```json
{
    "query":{
        "match":{
            "category":"小米"
        }
    }
}
```

这里我测试发现紫米，黑米，红米，都能查到，估计是对 **米** 做了分词

##### **3)** **多字段匹配查询**

multi_match 与 match 类似，不同的是它可以在多个字段中查询。

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "query": {
    "multi_match": {
        "query": "小米",
        "fields": ["title","category"]
    	}
    }
}
```

##### **4)** **关键字精确查询**

term 查询，精确的关键词匹配查询，不对查询条件进行分词。

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

**query String**

```json
{
    "query":{
        "match_phrase":{
			"category":"小米"
        }
    }
}
```

**<font color='bb000'>此时和上面不同，不会因为分词把近似值都查到，只能查到完全准确的答案</font>**

##### **5)** **多关键字精确查询**

terms 查询和 term 查询一样，但它允许你指定多值进行匹配。

如果这个字段包含了指定值中的任何一个值，那么这个文档满足条件，类似于 mysql 的 in 

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "query": {
        "bool":{
            "should":[ 
                {
                    "match_phrase":{
                        "category":"小米"
                    }
                },
                {
                    "match_phrase":{
                        "category":"黑米"
                    }
                }
            ]
        }
    }
}
```

##### **6)** **指定查询字段**

默认情况下，Elasticsearch 在搜索的结果中，会把文档中保存在_source 的所有字段都返回。

如果我们只想获取其中的部分字段，我们可以添加_source 的过滤

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "_source": ["title","price"], 
    "query": {
        "terms": {
        "category": ["小米"]
    	}
    }
}
```

##### **7)** **过滤字段**

我们也可以通过：

- includes：来指定想要显示的字段

- excludes：来指定不想要显示的字段

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "_source": {
    "excludes": ["category","price"]
    }, 
    "query": {
        "terms": {
        	"category": ["小米"]
    	}
    }
}
```

##### **8)** **组合查询**

`bool`把各种其它查询通过`must`（必须 ）、`must_not`（必须不）、`should`（应该）的方式进行组合

在 Postman 中，向 ES 服务器发 **GET** 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "query": {
        "bool": {
            "must": [
                {
                    "match": {
                    "category": "小米"
                        }
                    }
            ],
            "must_not": [
                {
                    "match": {
                    "price": "3000"
                        }
                    }
            ],
            "should": [
                {
                    "match": {
                    "title": "小米手机"
                        }
                    }
            ]
        }
    }
}
```

##### **9)** **范围查询**

range 查询找出那些落在指定区间内的数字或者时间。range 查询允许以下字符

| 操作符 | 说明       |
| ------ | ---------- |
| gt     | 大于>      |
| gte    | 大于等于>= |
| lt     | 小于<      |
| lte    | 小于等于<= |

在 Postman 中，向 ES 服务器发 **GET** 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "query": {
        "range": {
            "price": {
                "gte": 2000,
                "lte": 4000
                }
            }
    }
}
```

##### **10)** **模糊查询**

返回包含与搜索字词相似的字词的文档。

编辑距离是将一个术语转换为另一个术语所需的一个字符更改的次数。这些更改可以包括：

- 更改字符（box → fox）

- 删除字符（black → lack）

- 插入字符（sic → sick）

- 转置两个相邻字符（act → cat）

为了找到相似的术语，fuzzy 查询会在指定的编辑距离内创建一组搜索词的所有可能的变体或扩展。然后查询返回每个扩展的完全匹配。

通过 fuzziness 修改编辑距离。一般使用默认值 AUTO，根据术语的长度生成编辑距离。

在 Postman 中，向 ES 服务器发 **GET** 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "query": {
    	"fuzzy": {
    		"category": {
                "value": "小米"
            }
    	}
    }
}
```

```json
{
    "query": {
    	"fuzzy": {
    		"category": {
                "value": "小米",
                "fuzziiness": 2
            }
    	}
    }
}
```

##### **11) 分页查询文档**

```sh
http://127.0.0.1:9200/shopping/_search
```

**query String**

```json
{
    "query":{
        "match":{
            "category": "华为"
        }
    },
    "from" : 0,
    "size" : 2
}  
```

**只想查看指定属性**

**query String**

```json
{
    "query":{
        "match":{
            "category": "华为"
        }
    },
    "from" : 0,
    "size" : 2,
    "_source" : ["title"]
}  
```

**想对查询结果排序【单字段删除一个字段即可】**

**query String**

```json
{
    "query":{
        "match":{
            "category": "华为"
        }
    },
    "from" : 0,
    "size" : 2,
    "_source" : ["title", "price"],
    "sort": [
    	{
            "price": {
                "order":"desc"
            }
        },
        {
            "title": {
                "order":"asc"
            }
        }
    ]
}  
```

##### **13)** **高亮查询**

在进行关键字搜索时，搜索出的内容中的关键字会显示不同的颜色，称之为高亮。

Elasticsearch 可以对查询内容中的关键字部分，进行标签和样式(高亮)的设置。

在使用 match 查询的同时，加上一个 highlight 属性：

- pre_tags：前置标签

- post_tags：后置标签

- fields：需要高亮的字段

- title：这里声明 title 字段需要高亮，后面可以为这个字段设置特有配置，也可以空

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "query": {
    "match": {
    	"category": "小米"
    	}
    },
    "highlight": {
        "pre_tags": "<font color='red'>",
        "post_tags": "</font>",
        "fields": {
        "title": {}
    	}
    }
}
```

![15.png](./1 Elasticsearch 7.x.assets/15.png)

##### 14) 分页查询

from：当前页的起始索引，默认从 0 开始。 from = (pageNum - 1) * size

size：每页显示多少条

在 Postman 中，向 ES 服务器发 **GET** 请求 ：

```sh
http://127.0.0.1:9200/student/_search
```

```json
{
    "query": {
    	"match_all": {}
    	},
    "sort": [
        {
            "age": {
            "order": "desc"
        	}
    	}
    ],
    "from": 0,
    "size": 2
}
```

##### **15)** **聚合查询**

聚合允许使用者对 es 文档进行统计分析，类似与关系型数据库中的 group by，当然还有很多其他的聚合，例如取最大值、平均值等等。

- 对某个字段取最大值 max

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "aggs":{
    	"max_price":{
    		"max":{"field":"price"}
    	}
    },
    "size":0
}
```

- 对某个字段取最小值 min

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "aggs":{
    	"min_price":{
    		"min":{"field":"price"}
    	}
    },
    "size":0
}
```

- 对某个字段求和 sum

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "aggs":{
    	"sum_price":{
    		"sum":{"field":"price"}
    	}
    },
    "size":0
}
```

- 对某个字段取平均值 avg

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "aggs":{
    	"avg_price":{
    		"avg":{"field":"price"}
    	}
    },
    "size":0
}
```

- 对某个字段的值进行去重之后再取总数

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "aggs":{
    	"distinct_price":{
    		"cardinality":{"field":"price"}
    	}
    },
    "size":0
}
```

- State 聚合

  **对某个字段一次性返回 count，max，min，avg 和 sum 五个指标**

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "aggs":{
    	"stats_price":{
    		"stats":{"field":"price"}
    	}
    },
    "size":0
}
```

##### **16)** **桶聚合查询**

桶聚和相当于 sql 中的 group by 语句

- terms 聚合，分组统计

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "aggs":{
    	"age_groupby":{
    		"terms":{"field":"price"}
    	}
    },
    "size":0
}
```

- 在 terms 分组下再进行聚合

在 Postman 中，向 ES 服务器发 GET 请求 ：

```sh
http://127.0.0.1:9200/shopping/_search
```

```json
{
    "aggs":{
    	"age_groupby":{
    		"terms":{"field":"age"}
    	}
    },
    "size":0
}
```

### 2.2.5 Java API 操作

Elasticsearch 软件是由 Java 语言开发的，所以也可以通过 Java API 的方式对 Elasticsearch 服务进行访问

#### **2.2.5.1** **创建** **Maven** **项目**

我们在 IDEA 开发工具中创建 Maven 项目(模块也可)ES

修改 pom 文件，增加 Maven 依赖关系

```xml
<dependencies>
    <dependency>
        <groupId>org.elasticsearch</groupId>
        <artifactId>elasticsearch</artifactId>
        <version>7.8.0</version>
    </dependency>
    <!-- elasticsearch 的客户端 -->
    <dependency>
        <groupId>org.elasticsearch.client</groupId>
        <artifactId>elasticsearch-rest-high-level-client</artifactId>
        <version>7.8.0</version>
    </dependency>
    <!-- elasticsearch 依赖 2.x 的 log4j -->
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-api</artifactId>
        <version>2.8.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>2.8.2</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.9.9</version>
    </dependency>
    <!-- junit 单元测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
</dependencies>
```

#### **2.2.5.2** **客户端对象**

创建 **ESClient** 类，代码中创建 Elasticsearch 客户端对象因为早期版本的客户端对象已经不再推荐使用，且在未来版本中会被删除，所以这里我们采用高级 REST 客户端对象

```java
public class ESClient {
    public static void main(String[] args) throws IOException {

        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        // 关闭 ES 客户端
        esClient.close();
    }
}
```

**注意：9200** 端口为 Elasticsearch 的 Web 通信端口**，localhost** 为启动 ES 服务的主机名

#### **2.2.5.3** **索引操作**

ES 服务器正常启动后，可以通过 Java API 客户端对象对 ES 索引进行操作

##### **1)** **创建索引**

```java
public class ESClient {
    public static void main(String[] args) throws IOException {

        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        // 创建请求
        CreateIndexRequest request = new CreateIndexRequest("user");

        // 创建索引
        CreateIndexResponse createIndexResponse = 
            esClient.indices().create(request, RequestOptions.DEFAULT);

        // 响应状态
        boolean acknowledged = createIndexResponse.isAcknowledged();

        System.out.println("是否完成创建 : " + acknowledged);

        // 关闭 ES 客户端
        esClient.close();
    }
}
```

**去postman查询一下结果**

```sh
http://127.0.0.1:9200/_cat/indices?v 
```

##### **2)** **查看索引**

```java
public class ESClient {
    public static void main(String[] args) throws IOException {

        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        // 创建请求
        GetIndexRequest request = new GetIndexRequest("user");

        // 查询索引
        GetIndexResponse getIndexResponse =
                esClient.indices().get(request, RequestOptions.DEFAULT);

        // 响应状态
        System.out.println(getIndexResponse.getAliases());
        System.out.println(getIndexResponse.getSettings());
        System.out.println(getIndexResponse.getMappings());

        // 关闭 ES 客户端
        esClient.close();
    }
}
```

```sh
{user=[]}
{user={"index.creation_date":"1692021798782","index.number_of_replicas":"1","index.number_of_shards":"1","index.provided_name":"user","index.uuid":"xVOTLgviSVKQlHK7yf6eiQ","index.version.created":"7080099"}}
{user=org.elasticsearch.cluster.metadata.MappingMetadata@e1cbb692}
```

##### **3)** **删除索引**

```java
public class ESClient {
    public static void main(String[] args) throws IOException {

        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        // 创建请求
        DeleteIndexRequest request = new DeleteIndexRequest("user");

        // 查询索引
        AcknowledgedResponse acknowledgedResponse = esClient.indices().delete(request, RequestOptions.DEFAULT);

        // 响应状态
        System.out.println(acknowledgedResponse.isAcknowledged());

        // 关闭 ES 客户端
        esClient.close();
    }
}
```

**去postman查询一下结果**

```sh
http://127.0.0.1:9200/_cat/indices?v 
```

**发现没了**

#### **2.2.5.4** **文档操作**

##### **1)** **新增文档**

创建数据模型

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@ToString
public class User {
    private String name;
    private Integer age;
    private String sex;
}
```

创建数据，添加到文档中

```java
public class ESClientAdd {

    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        // 创建请求
        IndexRequest indexRequest = new IndexRequest();

        // 设置索引和唯一性id
        indexRequest.index("user").id("1001");

        // 创建数据对象
        User user = new User("wxf", 23, "男");
        ObjectMapper objectMapper = new ObjectMapper();
        String productJson = objectMapper.writeValueAsString(user);

        // 添加文档数据，数据格式为 JSON 格式
        indexRequest.source(productJson, XContentType.JSON);

        // 客户端发送请求获取响应对象
        IndexResponse response = esClient.index(indexRequest, RequestOptions.DEFAULT);

        // 响应状态
        System.out.println("response.getIndex() = " + response.getIndex());
        System.out.println("response.getId() = " + response.getId());
        System.out.println("response.getResult() = " + response.getResult());

        // 关闭 ES 客户端
        esClient.close();
    }
}
```

```apl
response.getIndex() = user
response.getId() = 1001
response.getResult() = CREATED
```

##### **2)** **修改文档**

```java
public class ESClientUpdate {

    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        // 创建请求
        UpdateRequest request = new UpdateRequest();

        // 设置索引和唯一性id
        request.index("user").id("1001");
        request.doc(XContentType.JSON, "sex", "女");

        // 客户端发送请求获取响应对象
        UpdateResponse response = esClient.update(request, RequestOptions.DEFAULT);

        // 响应状态
        System.out.println("response.getResult() = " + response.getResult());

        // 关闭 ES 客户端
        esClient.close();
    }
}
```

```apl
response.getResult() = UPDATED
```

##### **3)** **查询文档**

**这里默认其实 `Source` 默认是一个 `Map` 集合，我们打印为了直观可以转化为字符串**

```java
public class ESClientQuery {

    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        // 创建请求
        GetRequest request = new GetRequest();

        // 设置索引和唯一性id
        request.index("user").id("1001");

        // 客户端发送请求获取响应对象
        GetResponse response = esClient.get(request, RequestOptions.DEFAULT);

        // 响应状态
        System.out.println("response.getResult() = " + response.getIndex());
        System.out.println("response.getType() = " + response.getType());
        System.out.println("response.getSourceAsString() = " + response.getSourceAsString());

        // 关闭 ES 客户端
        esClient.close();
    }
}
```

```apl
response.getResult() = user
response.getType() = _doc
response.getSourceAsString() = {"name":"wxf","age":23,"sex":"女"}
```

##### **4)** **删除文档**

```java
public class ESClientDelete {
    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        // 创建请求
        DeleteRequest request = new DeleteRequest();

        // 设置索引和唯一性id
        request.index("user").id("1001");

        // 客户端发送请求获取响应对象
        DeleteResponse response = esClient.delete(request, RequestOptions.DEFAULT);

        // 响应状态
        System.out.println("response.getResult() = " + response.getResult());

        // 关闭 ES 客户端
        esClient.close();
    }
}
```

```apl
response.getResult() = DELETED
```

**可以在postman查**

```apl
http://127.0.0.1:9200/user/_doc/1001
```

##### **5)** **批量操作**

**批量添加**

```java
public class ESClientAddBatch {
    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        // 创建请求
        BulkRequest request = new BulkRequest();

        for (int i = 2; i <= 4; i++) {
            // 创建数据对象
            User user = new User("用户" + i, new Random().nextInt(30), "男");
            ObjectMapper objectMapper = new ObjectMapper();
            String productJson = objectMapper.writeValueAsString(user);

            // 添加文档数据，数据格式为 JSON 格式
            request.add(new IndexRequest()
                        .index("user")
                        .id(String.valueOf(1000 + i))
                        .source(productJson, XContentType.JSON));
        }


        // 客户端发送请求获取响应对象
        BulkResponse response = esClient.bulk(request, RequestOptions.DEFAULT);

        // 响应状态
        System.out.println("response.getTook() = " + response.getTook());
        System.out.println("response.getItems() = " + response.getItems());

        // 关闭 ES 客户端
        esClient.close();
    }
}
```

**批量删除同理，只需要id即可删除，不需要填入数据**

```java
            // 添加文档数据，数据格式为 JSON 格式
            request.add(new DeleteRequest()
                        .index("user")
                        .id(String.valueOf(1000 + i));
```

#### **2.2.5.5** **高级查询**

**数据准备**

```java
        for (int i = 1; i <= 5; i++) {
            // 创建数据对象
            String sex = i % 2 == 0 ? "男" : "女";
            User user = new User("用户" + i, new Random().nextInt(30), sex);
            ObjectMapper objectMapper = new ObjectMapper();
            String productJson = objectMapper.writeValueAsString(user);

            // 添加文档数据，数据格式为 JSON 格式
            request.add(new IndexRequest()
               	   .index("user")
               	   .id(String.valueOf(1000 + i))
        		   .source(productJson, XContentType.JSON));
        }
```

##### **1)** **请求体查询**

###### **查询所有索引数据**

**matchQuery分词搜索，termQuery完全匹配搜索**

```java
public class ESClientSearch {

    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        SearchRequest request = new SearchRequest();
        request.indices("user");

        request.source(new SearchSourceBuilder().query(QueryBuilders.matchAllQuery()));

        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();

        System.out.println("hits.getTotalHits() = " + hits.getTotalHits());
        System.out.println("response.getTook() = " + response.getTook());

        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
        
        // 关闭 ES 客户端
        esClient.close();
    }
}
```

```apl
hits.getTotalHits() = 5 hits
response.getTook() = 2ms
{"name":"用户1","age":19,"sex":"女"}
{"name":"用户2","age":16,"sex":"男"}
{"name":"用户3","age":26,"sex":"女"}
{"name":"用户4","age":28,"sex":"男"}
{"name":"用户5","age":10,"sex":"女"}
```

###### **term 查询，查询条件为关键字**

```java
public class ESClientSearchTerm {
    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        SearchRequest request = new SearchRequest();
        request.indices("user");

        request.source(new SearchSourceBuilder().query(QueryBuilders.termQuery("sex", "男")));

        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();

        System.out.println("hits.getTotalHits() = " + hits.getTotalHits());
        System.out.println("response.getTook() = " + response.getTook());

        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }

        // 关闭 ES 客户端
        esClient.close();
    }
}
```

**这里查到条件为性别为男的用户**

```apl
hits.getTotalHits() = 2 hits
response.getTook() = 1ms
{"name":"用户2","age":16,"sex":"男"}
{"name":"用户4","age":28,"sex":"男"}
```

###### **分页查询**

```java
public class ESClientSearchFromSize {
    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        SearchRequest request = new SearchRequest();
        request.indices("user");

        SearchSourceBuilder builder = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
        
        // 分页设置
        builder.from(0);
        builder.size(2);

        request.source(builder);

        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();

        System.out.println("hits.getTotalHits() = " + hits.getTotalHits());
        System.out.println("response.getTook() = " + response.getTook());

        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }

        // 关闭 ES 客户端
        esClient.close();
    }
}
```

```apl
hits.getTotalHits() = 5 hits
response.getTook() = 1ms
{"name":"用户1","age":19,"sex":"女"}
{"name":"用户2","age":16,"sex":"男"}
```

###### **数据排序**

```java
public class ESClientSearchSort {
    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        SearchRequest request = new SearchRequest();
        request.indices("user");

        SearchSourceBuilder builder = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
		
        // 设置排序字段和规则
        builder.sort("age", SortOrder.DESC);
		
        request.source(builder);

        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();

        System.out.println("hits.getTotalHits() = " + hits.getTotalHits());
        System.out.println("response.getTook() = " + response.getTook());

        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }

        // 关闭 ES 客户端
        esClient.close();
    }
}
```

```apl
hits.getTotalHits() = 5 hits
response.getTook() = 3ms
{"name":"用户4","age":28,"sex":"男"}
{"name":"用户3","age":26,"sex":"女"}
{"name":"用户1","age":19,"sex":"女"}
{"name":"用户2","age":16,"sex":"男"}
{"name":"用户5","age":10,"sex":"女"}
```

###### **过滤字段**

```java
    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        SearchRequest request = new SearchRequest();
        request.indices("user");

        SearchSourceBuilder builder = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());

        String [] includes = {};
        String [] excludes = {"age"};
        builder.fetchSource(includes, excludes);
        request.source(builder);

        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();

        System.out.println("hits.getTotalHits() = " + hits.getTotalHits());
        System.out.println("response.getTook() = " + response.getTook());

        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }

        // 关闭 ES 客户端
        esClient.close();
    }
```

**可以看到排除了 `age` 字段，`include` 同理**

```apl
hits.getTotalHits() = 5 hits
response.getTook() = 1ms
{"sex":"女","name":"用户1"}
{"sex":"男","name":"用户2"}
{"sex":"女","name":"用户3"}
{"sex":"男","name":"用户4"}
{"sex":"女","name":"用户5"}
```

###### **Bool 查询**

**should的意思是推荐，只会影响评分，不会影响查询结果**

```java
    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        SearchRequest request = new SearchRequest();
        request.indices("user");
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();

        boolQueryBuilder.must(QueryBuilders.matchQuery("age", "28"));
        // boolQueryBuilder.mustNot(QueryBuilders.matchQuery("age", "28"));
        // boolQueryBuilder.should(QueryBuilders.matchQuery("age", "28"));
        sourceBuilder.query(boolQueryBuilder);


        request.source(sourceBuilder);

        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();

        System.out.println("hits.getTotalHits() = " + hits.getTotalHits());
        System.out.println("response.getTook() = " + response.getTook());

        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }

        // 关闭 ES 客户端
        esClient.close();
    }
```

```apl
hits.getTotalHits() = 1 hits
response.getTook() = 3ms
{"name":"用户4","age":28,"sex":"男"}
```

###### **范围查询**

```java
    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        SearchRequest request = new SearchRequest();
        request.indices("user");
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("age");

        rangeQueryBuilder.gte(20);
        rangeQueryBuilder.lte(30);
        
        sourceBuilder.query(rangeQueryBuilder);

        request.source(sourceBuilder);

        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();

        System.out.println("hits.getTotalHits() = " + hits.getTotalHits());
        System.out.println("response.getTook() = " + response.getTook());

        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }

        // 关闭 ES 客户端
        esClient.close();
    }
```

```apl
hits.getTotalHits() = 2 hits
response.getTook() = 1ms
{"name":"用户3","age":26,"sex":"女"}
{"name":"用户4","age":28,"sex":"男"}
```

###### **模糊查询**

**中文自动分词 所以直接match就可以了**

```java
    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        SearchRequest request = new SearchRequest();
        request.indices("user");
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder()
                .query(QueryBuilders.fuzzyQuery("name", "用户").fuzziness(Fuzziness.ONE));

        request.source(sourceBuilder);

        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();

        System.out.println("hits.getTotalHits() = " + hits.getTotalHits());
        System.out.println("response.getTook() = " + response.getTook());

        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }

        // 关闭 ES 客户端
        esClient.close();
    }
```

##### **2)** **高亮查询**

**<font color='red'>注意：这里配置日志才能看到加了标签，要不然返回的结果集是不带的</font>**

```java
    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        SearchRequest request = new SearchRequest();
        request.indices("user");

        SearchSourceBuilder builder = new SearchSourceBuilder();

        HighlightBuilder highlightBuilder = new HighlightBuilder();
        highlightBuilder.preTags("<font color='red'>");
        highlightBuilder.postTags("</font>");
        highlightBuilder.field("age");

        builder.highlighter(highlightBuilder);

        builder.query(QueryBuilders.termQuery("age", "28"));

        request.source(builder);
        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();

        System.out.println("hits.getTotalHits() = " + hits.getTotalHits());
        System.out.println("response.getTook() = " + response.getTook());

        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }

        // 关闭 ES 客户端
        esClient.close();
    }
```

##### **3)** **聚合查询**

###### 最大年龄

**获取最大年龄【这里不加日志看不出来，但是也可以直接通过 `response` 的属性然后转化为`json` 串获取】**

```java
new ObjectMapper().writeValueAsString(searchResponse.getAggregations()))
```

```java
    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        SearchRequest request = new SearchRequest();
        request.indices("user");

        SearchSourceBuilder builder = new SearchSourceBuilder();

        builder.aggregation(AggregationBuilders.max("MaxAge").field("age"));

        request.source(builder);
        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        String str = new ObjectMapper().writeValueAsString(response.getAggregations());
        System.out.println(str);

        // 关闭 ES 客户端
        esClient.close();
    }
```

```json
{"asMap":{"MaxAge":{"name":"MaxAge","metadata":null,"value":28.0,"valueAsString":"28.0","type":"max","fragment":true}},"fragment":true}
```

###### 分组统计

**这里没法使用性别和名字，因为中文没有给分词，使用全部匹配 `keyword` 查询。**

```java
    public static void main(String[] args) throws IOException {
        // 创建 ES 客户端
        RestHighLevelClient esClient = new RestHighLevelClient(
                RestClient.builder(new HttpHost("localhost", 9200, "http"))
        );

        SearchRequest request = new SearchRequest();
        request.indices("user");

        SearchSourceBuilder builder = new SearchSourceBuilder();

        builder.aggregation(AggregationBuilders.terms("ageGroup").field("age"));

        request.source(builder);
        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);


        String str = new ObjectMapper().writeValueAsString(response.getAggregations());
        System.out.println(str);

        // 关闭 ES 客户端
        esClient.close();
    }
```

```json
{"asMap":{"ageGroup":{"name":"ageGroup","metadata":null,"buckets":[{"aggregations":{"asMap":{},"fragment":true},"keyAsString":"10","docCount":1,"docCountError":0,"key":10,"keyAsNumber":10,"fragment":true},{"aggregations":{"asMap":{},"fragment":true},"keyAsString":"16","docCount":1,"docCountError":0,"key":16,"keyAsNumber":16,"fragment":true},{"aggregations":{"asMap":{},"fragment":true},"keyAsString":"19","docCount":1,"docCountError":0,"key":19,"keyAsNumber":19,"fragment":true},{"aggregations":{"asMap":{},"fragment":true},"keyAsString":"26","docCount":1,"docCountError":0,"key":26,"keyAsNumber":26,"fragment":true},{"aggregations":{"asMap":{},"fragment":true},"keyAsString":"28","docCount":1,"docCountError":0,"key":28,"keyAsNumber":28,"fragment":true}],"type":"lterms","docCountError":0,"sumOfOtherDocCounts":0,"fragment":true}},"fragment":true}
```

# 第3章 Elasticsearch 环境

## **3.1** **相关概念**

### **3.1.1** **单机** **&** **集群**

单台 Elasticsearch 服务器提供服务，往往都有最大的负载能力，超过这个阈值，服务器性能就会大大降低甚至不可用，所以生产环境中，一般都是运行在指定服务器集群中。除了负载能力，单点服务器也存在其他问题：

- 单台机器存储容量有限

- 单服务器容易出现单点故障，无法实现高可用

- 单服务的并发处理能力有限

配置服务器集群时，集群中节点数量没有限制，大于等于 2 个节点就可以看做是集群了。一般出于高性能及高可用方面来考虑集群中节点数量都是 3 个以上。

**3.1.2** **集群** **Cluster**

一个集群就是由一个或多个服务器节点组织在一起，共同持有整个的数据，并一起提供索引和搜索功能。一个 Elasticsearch 集群有一个唯一的名字标识，这个名字默认就是”elasticsearch”。这个名字是重要的，因为一个节点只能通过指定某个集群的名字，来加入这个集群。

**3.1.3** **节点** **Node**

集群中包含很多服务器，一个节点就是其中的一个服务器。作为集群的一部分，它存储数据，参与集群的索引和搜索功能。

一个节点也是由一个名字来标识的，默认情况下，这个名字是一个随机的漫威漫画角色的名字，这个名字会在启动的时候赋予节点。这个名字对于管理工作来说挺重要的，因为在这个管理过程中，你会去确定网络中的哪些服务器对应于 Elasticsearch 集群中的哪些节点。

一个节点可以通过配置集群名称的方式来加入一个指定的集群。默认情况下，每个节点都会被安排加入到一个叫做“elasticsearch”的集群中，这意味着，如果你在你的网络中启动了若干个节点，并假定它们能够相互发现彼此，它们将会自动地形成并加入到一个叫做“elasticsearch”的集群中。

在一个集群里，只要你想，可以拥有任意多个节点。而且，如果当前你的网络中没有运行任何 Elasticsearch 节点，这时启动一个节点，会默认创建并加入一个叫做“elasticsearch”的集群。

## **3.2 Windows** **集群**

### **3.2.1** **部署集群**

1) 创建 elasticsearch-cluster 文件夹，在内部复制三个 elasticsearch 服务

 **这里直接删除之前的data和log文件夹，把之前残留的日志和数据删除**











































