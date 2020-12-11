rest风格是一种架构风格，而不是标准，只是提供了一组设计原则和约束条件。他主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制

| method |       url(localhost:9200+)        |          描述          |
| :----: | :-------------------------------: | :--------------------: |
|  PUT   |     /索引名称/类型名称/文档id     | 创建文档（指定文档id） |
|  POST  |        /索引名称/类型名称         | 创建文档（随机文档id） |
|  POST  | /索引名称/类型名称/文档id/_update |        修改文档        |
| DELETE |     /索引名称/类型名称/文档id     |        删除文档        |
|  GET   |     /索引名称/类型名称/文档id     |   通过文档id查询文档   |
|  POST  |    /索引名称/类型名称/_search     |      查询所有数据      |

### 关于索引的基本操作

#### 创建索引

1. 创建一个索引

    ```json
    PUT /test1/type1/1
    {
      "name": "你是谁",
      "age": 20
    }
    ```

2. 创建成功

    <img src="upload\image-20201208142308367.png" alt="image-20201208142308367" style="zoom:33%;" />

    自动增加索引，数据也成功的添加了。类似于数据库

3. name这个字段也要指定类型，毕竟我们是关系型数据库，是需要指定类型的。

    * 字符串类型

        text、keyword（不可分割）

    * 数值类型

        long、integer、short、byte、double、float、half float、scaled float

    * 日期类型

        date

    * 布尔值类型

        boolean

    * 二进制类型

        binary

    * 等等

4. 指定字段的类型

    ```json
    PUT /test2
    {
      "mappings": {
        "properties": {
          "name": {
            "type": "text"
          }, 
          "age": {
            "type": "integer"
          },
          "birthday": {
            "type": "date"
          }
        }
      }
    }
    ```

    查看这个规则的具体信息(GET 索引)

    ```json
    {
      "test2" : {
        "aliases" : { },
        "mappings" : {
          "properties" : {
            "age" : {
              "type" : "integer"
            },
            "birthday" : {
              "type" : "date"
            },
            "name" : {
              "type" : "text"
            }
          }
        },
        "settings" : {
          "index" : {
            "creation_date" : "1607409129699",
            "number_of_shards" : "1",
            "number_of_replicas" : "1",
            "uuid" : "CeI1or_rST6JP1vw3LDYug",
            "version" : {
              "created" : "7060099"
            },
            "provided_name" : "test2"
          }
        }
      }
    }
    ```

    <img src="upload\image-20201208144424219.png" alt="image-20201208144424219" style="zoom: 25%;" />

    如果自己的文档字段没有指定，那么es就会给我们配置默认字段类型

    扩展：通过命令elasticsearch查看索引情况

    ```
    GET _cat/*  #可以获得es当前的很多信息
    ```

#### 修改信息

1. 可以使用PUT修改，然后覆盖

    ```json
    PUT /test3/_doc/1
    {
      "name":"栾云平",
      "age": 36,
      "birth":"1984-03-20"
    }
    ```

    ```json
    {
      "_index" : "test3",
      "_type" : "_doc",
      "_id" : "1",
      "_version" : 2, //version变成了2
      "result" : "updated",
      "_shards" : {
        "total" : 2,
        "successful" : 1,
        "failed" : 0
      },
      "_seq_no" : 1,
      "_primary_term" : 1
    }
    ```

2. 通过POST+update修改

    ```json
    POST /test3/_doc/1/_update
    {
      "doc":{
        "name":"高栾是真的"
      }
    }
    ```

#### 删除索引

通过DELETE命令实现删除、根据你的请求来判断是删除索引还是删除文档记录

使用RESTFUL风格是我们ES推荐大家使用的

### 关于文档的基本操作

#### 基本操作

1. 添加数据 PUT

    ```json
    PUT /lyp/user/1  //ID要改，不然就不是添加，而是修改
    {
      "name":"栾云平",
      "age":23,
      "desc":"相声界扛把子",
      "tags":["怼怼", "直男","脾气"]
    }
    ```

2. 获取数据 GET

    ```json
    GET lyp/user/1
    ```

3. 更新数据 PUT

    ```json
    {
      "_index" : "lyp",
      "_type" : "user",
      "_id" : "3",
      "_version" : 2, // 版本
      "result" : "updated", // 操作
      "_shards" : {
        "total" : 2,
        "successful" : 1,
        "failed" : 0
      },
      "_seq_no" : 6,
      "_primary_term" : 1
    }
    ```

4. POST _update 推荐使用这种更新方式

    ```json
    PUT /lyp/user/3
    {
      "doc":{
        "age": 37
      }
    }
    POST /lyp/user/3/_update
    {
      "doc":{
        "age": 36
      }
    }
    ```

5. 简单的搜索

    ```json
    GET lyp/user/_search?q=name:栾云平
    ```

    简单的条件查询，可以根据默认的映射规则，产生基本的查询

#### 复杂查询

```json
//查询的参数体使用json结构
GET lyp/user/_search
{
  "query":{
    "match": {
      "name": "栾云"
    }
  }
}
```

结果：

```json
#! Deprecation: [types removal] Specifying types in search requests is deprecated.
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : { //hit索引和文档的信息
    "total" : { 
      "value" : 3,//查询的结果总数
      "relation" : "eq"
    },
    "max_score" : 1.2008023,//最大分数，我们可以通过分数来判断谁更加符合结果
    //查询出来的具体文档，遍历数据中的东西
    "hits" : [
      {
        "_index" : "lyp",
        "_type" : "user",
        "_id" : "1",
        "_score" : 1.2008023,
        "_source" : {
          "name" : "栾云平",
          "age" : 23,
          "desc" : "相声界扛把子",
          "tags" : [
            "怼怼",
            "直男",
            "脾气"
          ]
        }
      },
      {
        "_index" : "lyp",
        "_type" : "user",
        "_id" : "4",
        "_score" : 0.977973,
        "_source" : {
          "name" : "栾云平高峰",
          "age" : 23,
          "desc" : "相声界扛把子",
          "tags" : [
            "怼怼",
            "直男",
            "脾气"
          ]
        }
      },
      {
        "_index" : "lyp",
        "_type" : "user",
        "_id" : "5",
        "_score" : 0.977973,
        "_source" : {
          "name" : "栾云平爱徒",
          "age" : 23,
          "desc" : "相声界扛把子",
          "tags" : [
            "怼怼",
            "直男",
            "脾气"
          ]
        }
      }
    ]
  }
}
```

结果过滤

```json
"_source": ["name", "desc"]
```

排序

```json
//通过哪个字段进行排序
"sort": [
    {
        "age": {
            "order": "desc"
        }
    }
]
```

分页

```json
"from": 0, //从第几个数据开始
  "size": 1  //返回多少条数据
//数据下标从0开始
```

布尔值查询

must：下列的所有条件都要符合

should：只要满足下面任何条件一个都行

must_not：不满足以下条件的人（多个条件都不满足）

```json
GET lyp/user/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "栾云平"
          }
        },
        {
          "match": {
            "age": "23"
          }
        }
      ]
    }
  }
}
```

过滤

```json
"filter": {
    "range": {
        "age": {
            "gt": 22,
            "lt": 30
        }
    }
}
/**
gt 大于
lt 小于
get 大于等于
lte 小于等于
*/
```

匹配多个条件

```json
GET lyp/user/_search
{
  "query": {
    "match": {
      "tags": "好 有"
    }
  }
}
/*
多个条件使用空格隔开
只要满足其中一个结果就可以被查询出来
这个时候可以通过分值进行基本的判断
*/
```

精确查询

term查询是直接通过倒排索引指定的词条进行进程精确查找的

##### 关于分词

* term：直接查询精确的
* match：会使用分词器解析（先分析文档，然后再通过分析的文档进行查询)

两个类型：text keyword

```json
GET _analyze
{
  "analyzer": "keyword",//当成关键字去分析，不会被分词器解析
  "text": "陈豪是傻逼 java name"
}
GET _analyze
{
  "analyzer": "standard",
  "text": "陈豪是傻逼 java name"
}
```

多个值匹配精确查询

```json
GET testdb/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "term": {
            "t1": "22"
          }
        },
        {
          "term": {
            "t1": "33"
          }
        }
      ]
    }
  }
}
```

高亮查询

```json
"highlight": {
    //自定义搜索高亮条件
    "pre_tags": "<p class='key' style='color:red'>", 
    "post_tags": "</p>", 
    "fields": {
      "name":{}
    }
  }
```

