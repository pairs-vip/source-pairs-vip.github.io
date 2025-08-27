---
tags:
  - elasticsearch
  - kibana
categories:
  - ops
author: zmu
top: false
cover: /images/default-cover.jpg
toc: true
comments: true
date: 2025-08-26 16:32:00
updated: 2025-08-26 16:32:00
title: Kibana基本操作语法
---

# Kibana基本操作语法

## 1.Kibana相关了解

***kibana是es的查询工具，kibana默认端口是5601***
***访问地址一般为：http://ip:5601/kibana***

访问后登录账号密码为elasticsearch的账号密码

大多数时候使用的是kibana的 Dev Tools工具，具体位置位于  **kibana首页左上角更多 》 Management 》 Dev Tools**，一般在Dev Tools执行关于索引以及文档的操作。

## 2.Restful风格

基本Rest命令说明：

| method   | url地址                                                   | 描述                                                         |
| -------- | --------------------------------------------------------- | ------------------------------------------------------------ |
| PUT      | localhost:9200/索引名称                                   | 创建索引                                                     |
| PUT      | localhost:9200/索引名称/类型名称/文档id                   | 创建文档（指定文档id）                                       |
| POST     | localhost:9200/索引名称/类型名称                          | 创建文档（随机文档id）                                       |
| PUT      | localhost:9200/索引名称/类型名称/文档id {"name": "John" } | 创建文档（指定文档id和内容）                                 |
| POST     | localhost:9200/索引名称/类型名称/文档id/_update           | 修改文档                                                     |
| DELETE   | localhost:9200/索引名称/类型名称/文档id                   | 删除文档                                                     |
| GET      | localhost:9200/索引名称/类型名称/文档id                   | 查询文档通过文档id                                           |
| **POST** | localhost:9200/索引名称/类型名称**/_search**              | 查询所有数据                                                 |
| **GET**  | localhost:9200/索引名称**/_search**?q=identifier:CS       | 条件查询文档（查询所有索引下的满足identifier为CS的文档）索引名称非必须 |

注意：**对于 `_search` 端点，GET 和 POST 请求在功能上是完全一样的，可以互换使用。** 这是 Elasticsearch RESTful API 一个比较特殊但非常实用的设计。

**如果是使用Query DSL的复杂查询，一般使用POST加Body的方式查询，例如：**

```json
url: POST  localhost:9200/<index_name>/_search
<index_name>: 你要查询的索引名称。支持通配符和多个索引;查询单个索引：my_index;查询多个索引：index1,index2
查询所有索引：_all 或直接用 _search
_search: Elasticsearch 固定的搜索端点，表示我们要执行搜索操作。
body: {
  		"query": {
    		"match": {
      			"description": "red apple" // 在 "description" 字段中搜索 "red" 或 "apple"
    		}
  		}
	}
```

## 3.关于索引的基本操作

**注意：在kibana中执行操作索引以及文档的操作都是通过在Dev Toos控制台输入指令，然后选中点击右边的绿三角执行来实现的；执行结果位于右边板块中。**

### 3.1创建

#### **(1) 创建索引**

```json
PUT /test1
```

#### **(2) 创建索引规则**

```json
PUT /test2
{
    "mappings":	{
        "properties": {
            "name":{
                "type":"test"
            },
            "age":{
                "type":"long"
            },
            "sex":{
      			"type":"text"
      		}
        }
    }
}
```

#### **(3) 创建文档自动创建不存在的索引**

```json
POST /test3/_doc/zxf
{
    "name":"hello",
    "year":"2022",
    "month":1,
    "date":23
}
```

***第一种方式创建索引，没有设置索引的字段；第二种则是提前设置好了索引的字段；第三种则是通过创建文档动态创建索引以及索引字段，如果多次创建的文档字段不一样，索引则会通过扩展的方式创建字段，以确保包含所有文档的所有字段。***

### 3.2GET获取信息

#### **(1) 获取索引信息**

```json
GET test1
```

在返回结果中可以看到test1索引的相关信息，包括字段等。

#### **(2) 获取文档信息**

```
GET /test3/_doc/zxf
```

在返回结果中可以看到zxf文档的相关信息，包括字段的值等。

#### **(3) 其他常用查询指令**

查询集群健康值：

```
GET _cat/health
```

查询es包含的所有索引信息：

```
GET _cat/indices?v
```

### 3.3使用POST命令更新文档

```json
POST test3/_doc/zxf/_update
{
	"doc":{
		"name":"修改后的name"
	}
}
```

其中_update和doc都是固定的关键字：

| 关键字  | 说明       | 描述                                                         |
| ------- | ---------- | ------------------------------------------------------------ |
| _update | 端点部分   | `_update` 是 Elasticsearch 的**固定端点**，表示这是一个部分更新操作。 |
| doc     | 请求体部分 | 在请求体内的 `"doc": {...}` 也是一个**固定结构**，表示你要更新的字段和值。 |

### 3.4删除

#### **(1) 删除索引**

```json
DELETE test2
```

#### **(2) 删除索引中的某个文档**

```json
DELETE test3/_doc/zxf
```

## 4.关于文档的基本操作

### 4.1PUT/POST创建文档

```json
PUT test1/_doc/zxf
{
  "name":"zxf",
  "year":2022,
  "month":1,
  "date":25
}
 
PUT test1/_doc/wanna_sleep
{
  "name":"wanna sleep",
  "year":2022,
  "month":1,
  "date":25
}
```

### 4.2GET获取文档

#### **(1) 获取指定文档**

```json
GET test1/_doc/wanna_sleep
```

#### **(2) 获取所有文档**

```json
GET _search
{
  "query": {
    "match_all": {}
  }
}
```

### 4.3POST更新文档

见 3.3使用POST命令更新文档

### 4.4kibana简单查询

**在索引中根据关键字搜索文档**

```json
# 搜索类型为type1的索引test1中，name字段值为zxf的文档
GET test1/type1/_search?q=name:zxf

# 搜索类型为type1的索引test1中，year字段值为2022的文档
GET test1/type1/_search?q=year:2022
```

***这里存在一个问题， 如果没有给es安装分析器插件的话，就不能实现部分匹配，比如说通过name字段搜索，必须在命令中给出完整name才能匹配到。*** 

### 4.5kibana复杂查询

#### **(1) 模糊查询**

```
match是模糊查询：输入“敬业福”，相当于SQL语句的 WHERE name LIKE '%敬业福%' OR LIKE '%敬业%' OR LIKE '敬' OR LIKE '业'（和SQL不同的是，es的模糊查询会分词）

term是精确查询：输入“我想要敬业福”，相当于SQL语句的 WHERE name = '我想要敬业福'
```

```json
GET test1/type1/_search
{
  "query":{
    "match": {
      "name": "福"
    }
  }
}
```

#### **(2) 过滤不想看的字段**

如果只想看查询结果的name字段和year字段，不想看其他month、date字段，可以使用**过滤器**。

```json
GET test1/type1/_search
{
  "query":{
    "match": {
      "name": "福"
    }
  },
  "_source":["name","year"]
}
```

#### **(3) 排序**

实现先按照月份再按照日期的倒序排序：

```json
GET test1/type1/_search
{
  "query":{
    "match": {
      "name": "福"
    }
  },
  "sort":[
    {
      "month":{
        "order":"desc"  # 升序是"asc"
      },
      "date":{
        "order":"desc"
      }
    }
  ]
}
```

#### **(4) 分页查询**

```json
GET test1/type1/_search
{
  "query":{
    "match": {
      "name": "福"
    }
  },
  "_source":["name","month"],
  "sort":[
    {
      "month":{
        "order":"desc"
      }
    }
  ],
  "from":0,
  "size":3
}
```

**from：从查询结果的第几个开始显示**

**size：一个页面显示几个**

**注意，返回的查询结果下标从0开始。**

#### **(5) 布尔值bool查询**

布尔查询是一个或多个查询子句的组合，每一个子句就是一个子查询。子查询的组合方式有：
**must:必须匹配每个子查询，类似于“与”							AND**
**should：选择性匹配子查询，类似于“或”						OR**
**must_not：必须不匹配，不参与算分，类似于“非”			NOT**
**filter：必须匹配，参与算分**

必须匹配：must/filter
必须不匹配：must_not
选择性匹配：should

```json
GET /index_name/_search
{
	"query":{
		"bool":{
			"must":[
				{"match":{"属性标识":"属性值"}},
				{"match":{"属性标识1":"属性值"}}
			],
			"should":[
			],
			"must_not":[
			],
			"filter":[
			]
		}
	}
}
```

例如：查询名字里面带 "福" 的，并且month小于等于4，大于等于3的文档


```json
GET test1/type1/_search
{
  "query":{
    "bool": {
      "must": [
        {
          "match": {
            "name": "福"
          }
        }
      ],
      "filter": [
        {
          "range": {
            "month": {
              "gte": 3,
              "lte": 4
            }
          }
        }
      ]
    }
  }
}
```

#### (6) 模糊查询的多条件查询

多个条件直接用“空格”隔开

```json
GET test1/type1/_search
{
  "query":{
    "match": {
      "name": "福 敬业 wanna"
    }
  }
}
```

#### (7) 精确查询

如果此时查询wanna，查询结果为0

```json
GET test1/type1/_search
{
  "query":{
    "term": {
      "name": "wanna sleep"
    }
  }
}
```

#### (8)精确查询的多条件查询

模糊查询的多条件查询直接用“空格”隔开就行了，但是精确查询不能直接用空格隔开。

```
GET test1/type1/_search
{
 "query":{
   "bool": {
     "must": [
       {
         "term": {
           "month": 4
         }
       },
       {
         "term": {
           "date": 1
         }
       }
     ]
   }
 }
}
```

#### (9) 多字段模糊查询 match_multip

根据多个字段查询这个关键字，只要其中一个字段匹配到了这个关键字就可以查出来

```json
GET /index_name/_search
{
	"query":{
		"multi_match":{
			"query":"查询条件",
			"fields":["属性标识1","属性标识2"]
		}
	}
}
```

只要查询条件满足属性标识1或者属性标识2中的任意一个，就可以查询出来

#### (10) 短语模糊查询match_prase

**如果是keyword类型，等同于精确查询，必须要完全匹配才行**
**如果是text类型，会对搜索条件进行分词，而且要保持相对位置一直才能被搜索到。而且分出来的词要完全匹配**
比如，有一条数据：name : i like study es;
搜索 like study 可以搜到该结果
但是搜索 like es 搜不到，因为相对位置不一样了，条件中like 和 es挨着，但是数据中like 和 es 中间存在study；
搜索like es 和 like ？？？？++es 搜索结果一样

#### (11) 单字整体模糊匹配 wildcard 查询，类似sql的like查询

```json
GET /index_name/_search
{
	"query":{
		"wildcard":{
			"属性标识":"*性值"
		}
	}
}
```