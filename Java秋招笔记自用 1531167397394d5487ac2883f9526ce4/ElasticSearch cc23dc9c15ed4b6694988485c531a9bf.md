# ElasticSearch

# 基础

## 概念

### 什么是ES？

一个用java语言开发的基于Lucene开源的分布搜索引擎，基于开源搜索引擎内库，用于实时存储、搜索和分析大规模数据集。

Lucene是一个Java编写的高性能全功能文本搜索引擎库，提供强大的索引功能，直接基于Lucene开发非常复杂。ES封装了很多Lucene的底层功能，提供了易用的接口和多语言客户端。

### 正向索引和倒排索引有什么区别？

- 正向索引：基于文档id创建索引，查询词条时先找到文档，再判断是否包含词条。
    - 优点：易于维护，新增数据时只需要在尾部新增一个ID；
    - 缺单：查询效率低。
- 倒排索引：对文档内容进行分词，对词条创建索引，并记录词条所在文档的信息。查询时先根据词条查询到文档id，而后获取文档。
    - 优点：查询效率高；
    - 缺点：不易维护，建立倒排索引的成本高，每次更新都要重建倒排索引。

### MySQL和ES的概念对比？

![Untitled](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/Untitled.png)

- MySQL是关系型数据库管理系统，ES是分布式搜索引擎；
- MySQL使用SQL，ES使用DSL（Domain Specific Language）；
- MySQL使用正向索引的索引方式，ES使用了倒排索引；
- MySQL擅长事务操作，ES擅长数据的搜索分析和运算。

### 为什么不用关系型数据库做搜索？

- 数据量大时，性能严重下降，尤其是非左匹配的模糊查询索引会失效；
- 难以应对高并发的场景，ES能够方便地增加节点来分散数据负载，提高系统的性能和可扩展性；
- 无法应对复杂的业务查询，而ES支持全文搜索、聚合查询等功能。

## Mapping

Mapping类似于数据库的表结构，可以增加不能修改，否则只能重新索引。一个Index对应一个Mapping。

Mapping分为动态Mapping和显示Mapping两种：

- 动态Mapping：根据待索引数据自动建立索引、自动定义映射类型。
- 显示 Mapping：手动控制字段的存储和索引方式比如哪些字符串字段应被视为全文字段。

### Mapping常见的属性有哪些？

- type：数据类型；
    - 字符串：text、keyword（不分词）
    - 数字：byte、short、int、long、float、double
    - 布尔：boolean
    - 日期：date
    - 对象：object
- index：是否是索引，不是索引不能被检索，但结果中还是包含；
- analyzer：分词器；
- properties：子字段。

### text和keyword有什么区别？

keyword不走分词器，查询效率更高。text走分词器。

### 有自定义过Mapping吗？

是自定义的，这样更加准确一些。否则动态的Mapping还会带来映射爆炸等其他的问题。如果数据很复杂，可以采用下面的方法：

1. 创建临时的Index，插入一些数据；
2. 获取动态生成的Mapping定义；
3. 在Mapping的基础上进行修改；
4. 删除临时的Index。

## 基本操作

### 索引库操作

```java
# 创建索引库
put /索引库名

# 查询索引库
get /索引库名

# 删除索引库
delete /索引库名

# 添加字段
put /索引库名/_mapping
```

### 文档操作

```java
# 创建文档
POST /索引库名/_doc/文档id {json文档}

# 查询文档
GET /索引库名/_doc/文档id

# 删除文档
DELETE /索引库名/_doc/文档id

# 修改文档
# 全量修改
PUT /索引库名/_doc/文档id {json文档}
# 增量修改
POST /索引库名/_update/文档id {"doc": {字段}}
```

### java客户端操作

1. 引入依赖`elasticsearch-rest-hight-level-client` 。
2. 初始化：
    
    ```java
    @Bean
    public RestHightLevelClient restHightLevelClient(){
    		return new RestHightLevelClient(RestClient.builder(HttpHost.create("http://xxx.xxx.xxx.xxx:9200")));
    }
    ```
    
3. 索引库操作：创建xxxIndexRequest，xxx是create/get/delete。
    
    ![Untitled](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/Untitled%201.png)
    
    ![Untitled](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/Untitled%202.png)
    
4. 文档操作：创建xxxRequest，xxx是index/get/delete/update。
    
    ![Untitled](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/Untitled%203.png)
    
    ![Untitled](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/Untitled%204.png)
    
    ![Untitled](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/Untitled%205.png)
    
    ![Untitled](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/Untitled%206.png)
    
5. 批量文档处理：
    1. 查询所有数据；
    2. 将查询到的数据转换为文档类型数据
    3. 利用javaRestClient中的Bulk批处理，实现文档的新增操作
    
    ![Untitled](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/Untitled%207.png)
    

# DSL查询

## 查询种类

- 查询所有：查询出所有数据，一般测试用，如match_all。
- 全文检索：分词器对用户输入分词，然后去倒排索引库中匹配。
- 精确查询：根据精确词条值查找数据，一般是keyword、数值、日期等。
- 地理查询：根据经纬度查询。
- 复合查询：将上述各种查询条件组合起来，合并查询条件。

### 精确查询和全文检索的区别？

1. term查询条件不做分词处理，精确匹配才会被搜索到，一般用于非文本字段查询；
2. 全文查询一般用于文本查询，会分词→按词条逐个查询→汇总得分。

### 全文查询中match和match_phrase的区别？

用于单字段查询，match多个检索词之间默认是or关系，match_phrase默认是and关系，并且词的位置影响最终的检索结果。

### 全文查询中multi_match的检索策略？

用于多字段查询，匹配策略有：

- best_fields默认：查询的最终的得分是最佳字段的得分；
- most_fields：最终得分是所有匹配字段得分的合并；
- cross_fields：将多个字段整合为一个字段计算得分。

## 查询语法

```java
GET /indexName/_search
{
		"query": {
				"查询类型": {
						"查询条件": "条件值"
				}
		}
}
```

### 全文检索

```java
# 单字段查询match
GET /indexName/_search
{
		"query": {
				"match": {
						"FIELD": "TEXT"
				}
		}
}

# 多字段查询multi_match
GET /hotel/_search
{
		"query": {
				"multi_match": {
						"query": "TEXT",
						"fields": ["FIELD1", "FIELD2"]
				}
		}		
}
```

### 精确查询

```java
# 根据词条精确查询term
GET /indexName/_search
{
		"query": {
				"term": {
						"FIELD": {
								"value": "VALUE"
						}
				}
		}
}

# 根据值范围查询range
GET /indexName/_search
{
		"query": {
				"range": {
						"FIELD": {
								"gte": "VALUE1",
								"lt": "VALUE2"
						}
				}
		}
}
```

### 相关性算分与FunctionScore查询

使用match查询时，文档会根据与搜索词条的关联度进行打分（_score），返回结果时按照分值降序排列。（根据词条频率和逆文档频率（TF-IDF算法）或BM25算法计算）

- TF-IDF算法：随着词频的增大而越来越大；
- BM25算法：随着词频的增大，增长曲线趋于水平。

![Untitled](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/Untitled%208.png)

- 原始查询
- 过滤条件：哪些文档要加分
- 算分函数：如何计算分数

### 复合查询

![Untitled](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/Untitled%209.png)

## 查询结果处理

### 查询排序

```java
GET /indexName/_search
{
		"query": {
				"match_all": {}
		},
		"sort":[
				{"FIELD1": "desc"},
				{"FIELD2": "asc"},
				...
		]
}
```

### 查询分页

ES默认返回前10条数据。

```java
GET /indexName/_search
{
		"query": {
				"match_all": {}
		},
		"from": NUM,
		"size": NUM
}
```

深度分页问题

![Untitled](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/Untitled%2010.png)

![Untitled](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/Untitled%2011.png)

### 高亮显示

```java
GET /indexName/_search
{
		"query": {
				"all": "酒店" // 查询字段
		},
		"hightlight": {
				"fields": {
						"name": { // 高亮字段
								"pre_tags": "<em>",
								"post_tags": "</em>"
								"require_field_match": "false" // 高亮字段与查询字段不一样需要添加
						}
				}
		}
}
```

# 数据聚合

聚合：对文档数据的统计、分析、计算。

## 聚合分类

- 桶（Bucket）聚合：用来对文档做分组。
    - TermAggregation：按文档字段值进行分组；
    - Date Histogram：按照日期阶梯进行分组。
- 度量（Metric）聚合：用以计算一些值，如最大值、最小值、平均值。
    - Avg
    - Max
    - Min
    - Stats：同时求max、min、avg、sum等
- 管道（Pipline）聚合：对其他聚合的结果做聚合。

![Untitled](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/Untitled%2012.png)

![Untitled](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/Untitled%2013.png)

# 自动补全

## 分词器

### 组成部分

分词器包含三个部分：

1. character filter：在Tokenizer之前对文本进行处理。例如：删除字符、替换字符。
2. tokenizer：将文本按照一定的规则切割成词条；
3. filter：将分词器输出的词条进行进一步处理。

### 自定义分词器

创建索引库的时候通过settings来配置自定义的analyzer。

![image.png](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/image.png)

- 拼音分词器适合在创建文档时使用，但不能在搜索时使用。（用户输入中文，搜索应该按中文来搜）应当指定search_analyzer。
    
    ![image.png](ElasticSearch%20cc23dc9c15ed4b6694988485c531a9bf/image%201.png)
    

## 自动补全查询

1. 参与补全查询的字段必须是Completion类型；
2. 字段的内容一般是用来补全的多个词条形成的数组。

# 数据同步

ES与MySQL的同步类型包括全量同步和增量同步。

- 全量同步：建好 Elasticsearch 索引后一次性导入 MySQL 所有数据，有很多中间件如go-mysql-elasticsearch可以实现。
- 增量同步：对 MySQL 中新增，修改，删除的数据进行同步：
    - 同步双写：修改数据时同步到ES，业务耦合；
    - 异步双写：修改数据时，使用MQ异步写入ES；
    - binlog同步组件Canal：解析binlog日志文件进行数据同步。