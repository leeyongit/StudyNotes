# Es高级用法

> 常用查询
>
> 全文本查询 针对文本数据类型
>
> 字段基本查询针对结构化数据，如数字，日期等
>

### match 查询

模糊匹配

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

### match_phrase 查询

单词匹配

```json
{
    "query":{
        "match_phrase":{
            "title":"大师傅"
        }
    }
}
```

###  multi_match 查询

`multi_match` 查询可以在多个字段上执行相同的 `match` 查询：

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

### query_string 查询

可以在查询里边使用AND或者OR来完成复杂的查询，例如：

```json
{
    "query":{
        "query_string":{
            "query":"(大师傅 ADN 芬法) OR 阿黑"
        }
    }
}
```

### term 查询

`term` 查询被用于精确值匹配，这些精确值可能是数字、时间、布尔或者那些 `not_analyzed` 的字符串：

查看指定的数据，书籍字数是1000

```json
{
    "query":{
        "term":{
            "word_count":1000
        }
    }
}
```

###  terms 查询

`terms` 查询和 `term` 查询一样，但它允许你指定多值进行匹配。如果这个字段包含了指定值中的任何一个值，那么这个文档满足条件：

```sh
{ "terms": { "tag": [ "search", "full_text", "nosql" ] }}
```

**range 查询**

`range` 查询找出那些落在指定区间内的数字或者时间：

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

被允许的操作符如下：

- **`gt`**

  大于

- **`gte`**

  大于等于

- **`lt`**

  小于

- **`lte`**

  小于等于

##  组合多查询

现实的查询需求从来都没有那么简单；它们需要在多个字段上查询多种多样的文本，并且根据一系列的标准来过滤。为了构建类似的高级查询，你需要一种能够将多查询组合成单一查询的查询方法。

你可以用 `bool` 查询来实现你的需求。这种查询将多查询组合在一起，成为用户自己想要的布尔查询。它接收以下参数：

- **`must`**

  文档 *必须* 匹配这些条件才能被包含进来。

- **`must_not`**

  文档 *必须不* 匹配这些条件才能被包含进来。

- **`should`**

  如果满足这些语句中的任意语句，将增加 `_score` ，否则，无任何影响。它们主要用于修正每个文档的相关性得分。

- **`filter`**

  *必须* 匹配，但它以不评分、过滤模式来进行。这些语句对评分没有贡献，只是根据过滤标准来排除或包含文档。

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

