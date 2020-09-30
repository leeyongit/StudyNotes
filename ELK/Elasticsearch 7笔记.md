# Elasticsearch 7 笔记

**查看所有索引**

```sh
GET _cat/indices?v
```

**查看hwyy_开头的索引**

```sh
GET _cat/indices/hwyy_*?v
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



 **"track_total_hits":true 的作用**

```json
# 获取超过1w条数据 需要加上  "track_total_hits":true ，不然只能显示出9999条
GET /pg03_h3/_doc/_search
{
  "_source":["terminalId",
              "lon",
              "lat",
              "time",
             "h3_2"
            ],
  "query": {"match": {
    "h3_2":"586321722337132543"
  }},
  "size":10000,
  "track_total_hits":true
```

