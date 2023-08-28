# IK分词

IK支持两种分词模式：

- ik_max_word: 会将文本做最细粒度的拆分，会穷尽各种可能的组合
- ik_smart: 会做最粗粒度的拆分

 ```json
POST /_analyze
{  
		"analyzer": "ik_max_word",
    "text": "世界如此之大"  
}

POST _analyze?pretty=true
{
		"tokenizer":"ik_smart"
		"text":"我们是软件工程师",
		
}
 ```

## 设置mapping默认分词器

示例：

```json
{
    "properties": {
        "content": {
            "type": "text",
            "analyzer": "ik_max_word",
            "search_analyzer": "ik_max_word"
        }
    }
}
```

注：这里设置 `search_analyzer` 与 `analyzer` 相同是为了确保搜索时和索引时使用相同的分词器，以确保查询中的术语与反向索引中的术语具有相同的格式。如果不设置 `search_analyzer`，则 `search_analyzer` 与 `analyzer` 相同。