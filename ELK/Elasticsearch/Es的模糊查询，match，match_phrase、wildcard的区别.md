# Es的模糊查询，match，match_phrase、wildcard的区别

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

