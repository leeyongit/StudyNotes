# Elasticsearch 7 笔记

## 关于 Index、Type、Document

- Index 对应 MySQL 中的 Database；
- ~~Type 对应 MySQL 中的 Table；~~
- Document 对应 MySQL 中表的记录。

在 7.0 以及之后的版本中 Type 被废弃了。（其实还可以用，但是已经不推荐了）

一个MySQL实例中可以创建多个 Database，一个Database中可以创建多个Table。

ES 的Type 被废弃后：

- ES 实例：对应 MySQL 实例中的一个 Database。
- Index 对应 MySQL 中的 Table 。
- Document 对应 MySQL 中表的记录。

> Index 的复数是 Indices。



**查看所有索引**

```sh
GET _cat/indices?v
```

**查看hwyy_开头的索引**

```sh
GET _cat/indices/hwyy_*?v
```

**查看 setting、mapping 信息**

```sh
GET movies/_doc/_mapping
```

## settings和 mapping 

**所谓的settings就是用来修改索引分片和副本数的;**
比如有的重要索引, 副本数很少甚至没有副本, 那么我们通过setting来添加副本数.

- 新建一个索引库 document

```json
DELETE document
PUT document
{
    "mappings":{
        "article":{
            "properties":{
                "title":{
                    "type":"text"
                },
                "author":{
                    "type":"text"
                },
                "titleScore":{
                    "type":"double"
                }
            }
        }
    }
}
```

- 查看副本数

```sh
GET /document/_settings
```

**可以看到当前的副本数是1**

- 那么为了提供容错性, 我们可以把副本数改为2;

```shell
PUT /document/_settings
{
  "number_of_replicas": 2
}
```

- 副本数能够修改,分片数无法修改,修改分片数会报错

```sh
PUT /document/_settings
{
  "number_of_shards": 3
}
```

### ES 7 中在创建索引时指定 Mapping

```json
PUT school
{
  "mappings" : {
    "properties" : {
      "name" : {
        "type" : "keyword"
      }
    }
  }
}
```

响应：

```json
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "school"
}
```

然后可以通过`GET school`查询索引信息。

### ES 7 中先建索引，再自定义 mapping

```sh
PUT school
```

```json
PUT school/_mapping
{
    "properties" : {
      "name" : {
        "type" : "keyword"
      }
    }
}
```

### ES 7 建索引时指定mapping和settings

比如创建索引时指定分片数和副本数：

```json
PUT school
{
  "mappings" : {
    "properties" : {
      "name" : {
        "type" : "keyword"
      }
    }
  },
  "settings" : {
    "index" : {
      "number_of_shards" : 1,
      "number_of_replicas" : 2
    }
  }
}
```

### ES 6 中在创建索引时指定 Mapping 和 Setting

因为 ES 7 之前的版本，一个index下支持多个type，所以`mappings` 下一层要指定 `type`。

```json
PUT school
{
  "mappings" : {
    "student": {
      "properties" : {
        "name" : {
          "type" : "keyword"
        }
      }
    }
  },
  "settings" : {
    "index" : {
      "number_of_shards" : 1,
      "number_of_replicas" : 2
    }
  }
}
```

# 字符串类型 keyword 、text

------

在 ElasticSearch 5.0 之前，字符串类型是 string。从 5.0 版本开始，string 类型被废弃，引入了 keyword 、text 两种类型。

### 两者的主要区别是：

- keyword 不支持全文搜索。所以，只能是使用精确匹配进行查询，比如 term 查询。
- text 默认支持全文搜索。

### 长度区别：

- keyword 的最长长度是 32766 字节。（原因应该是底层lucene做倒排索引时，限制了单词的长度。UTF-8中，英文字母是1个字节，中文一般是3个字节，表情符号是4个字节）
- text 无长度限制。（但被分析器处理后的单词不应该超过 32766 个字节。-> 这是我推论出来的，待验证）。



## 查询技巧

- es7.x在查询时，必须加上track_total_hits，不然就只显示10000   `"track_total_hits":true` 



