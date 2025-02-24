# ElasticSearch查询区别

## match、match_phrase、term区别

**term结构化字段查询，匹配一个值，且输入的值不会被分词器分词。**

比如查询条件是：

```json
{
    "query":{
        "term":{
            "foo": "hello world"
        }
    }
}
```

那么只有在字段中存储了“hello world”的数据才会被返回，如果在存储时，使用了分词，原有的文本“I say hello world”会被分词进行存储，不会存在“hello world”这整个词，那么不会返回任何值。

但是如果使用“hello”作为查询条件，则只要数据中包含“hello”的数据都会被返回，分词对这个查询影响较大。

**match_phase习语匹配，查询确切的phase，在对查询字段定义了分词器的情况下，会使用分词器对输入进行分词，然后返回满足下述两个条件的document:**

- match_phase中的所有term都出现在待查询字段之中
- 待查询字段之中的所有term都必须和match_phase具有相同的顺序

```json
{ "foo":"I just said hello world" }

{ "foo":"Hello world" }

{ "foo":"World Hello" }
```

使用match_phase:

```json
{
  "query": {
    "match_phrase": {
      "foo": "Hello World"
    }
  }
}
```

会返回前两条文档。

**match模糊匹配，先对输入进行分词，对分词后的结果进行查询，文档只要包含match查询条件的一部分就会被返回。**

**query_string语法查询，同match_phase的相同点在于，输入的查询条件会被分词，但是不同之处在与文档中的数据可以不用和query_string中的查询条件有相同的顺序。**

## Es的模糊查询，match，match_phrase、wildcard的区别

1. match查询：会先对搜索词进行分词，比如“白雪公主和苹果”，会分成“白雪”“公主”“苹果”。含有相关内容的字段，都会被检索出来。

2. match_phrase查询：match_phrase与slop一起用，能保证分词间的邻近关系，slop参数告诉match_phrase查询词条能够相隔多远时仍然将文档视为匹配，默认是0。为0时 必须相邻才能被检索出来。
   例如下面的语句，会把“白雪公主吃苹果”也能检索出来

```json
{
    "query":{
        "match_phrase":{
            "content":{
                "query":"白雪公主和苹果",
                "slop":1
            }
        }
    }
}
```

3. wildcard查询：是使用通配符进行查询，其中？代表任意一个字符

```json
{
    "query":{
        "wildcard":{
            "title":"*公主*"
        }
    }
}
```

