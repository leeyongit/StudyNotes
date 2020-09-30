# Es高级用法

常用查询

全文本查询 针对文本数据类型

字段基本查询针对结构化数据，如数字，日期等

**模糊匹配**

> post
>  http://127.0.0.1:9200/book/_search

```json
{
  "query":{
    "match":{
      "title":"的黑"
    }
  }
}
```

会匹配所有包含“的”，“黑”的内容

**单词匹配**

```json
{
    "query":{
        "match_phrase":{
            "title":"大师傅"
        }
    }
}
```

**多字段匹配**

```json
{
    "query":{
        "multi_match":{
            "query":"核",
            "fields":[
                "author",
                "title"
            ]
        }
    }
}
```

**多内容匹配**

```json
{
    "query":{
        "query_string":{
            "query":"(大师傅 ADN 芬法) OR 阿黑"
        }
    }
}
```

**查看指定的数据**
 书籍字数是1000

```json
{
    "query":{
        "term":{
            "word_count":1000
        }
    }
}
```

**查看指定范围的数据**

```json
{
    "query":{
        "range":{
            "word_count":{
                "gte":1000,
                "lte":2000
            }
        }
    }
}
```

**过滤查询 filter**

```sh
{
    "query":{
        "bool":{
            "filter":{
                "term":{
                    "word_count":1000
                }
            }
        }
    }
}
```

**固定分数查询**

```json
{
    "query":{
        "constant_socore":{
            "filter":{
                "match":{
                    "title":"ElasticSearch"
                }
            },
            "boost":2
        }
    }
}
```

**或的关系**

```json
{
    "query":{
        "bool":{
            "should":[
                {
                    "match":{
                        "author":"瓦力"
                    }
                },
                {
                    "match":{
                        "title":"ElasticSearch"
                    }
                }
            ]
        }
    }
}
```

**必须的**

```json
{
    "query":{
        "bool":{
            "must":[
                {
                    "match":{
                        "author":"瓦力"
                    }
                },
                {
                    "match":{
                        "title":"ElasticSearch"
                    }
                }
            ]
        }
    }
}
```

**组合查询**

```json
{
    "query":{
        "bool":{
            "must":[
                {
                    "match":{
                        "author":"瓦力"
                    }
                },
                {
                    "match":{
                        "title":"ElasticSearch"
                    }
                }
            ],
            "filter":[
                {
                    "term":{
                        "word_count":1000
                    }
                }
            ]
        }
    }
}
```

```json
{
    "query":{
        "bool":{
            "must_not":[
                {
                    "match":{
                        "author":"瓦力"
                    }
                },
                {
                    "match":{
                        "title":"ElasticSearch"
                    }
                }
            ],
            "filter":[
                {
                    "term":{
                        "word_count":1000
                    }
                }
            ]
        }
    }
}
```

