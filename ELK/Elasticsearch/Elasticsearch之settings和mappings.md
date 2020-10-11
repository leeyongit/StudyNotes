# Elasticsearch之settings和mappings

> **Elasticsearch之settings和mappings的意义**
>
> 简单的说，就是
>
> 　　settings是修改分片和副本数的。
>
> 　　mappings是修改字段和类型的。

## **1、ES中的settings**

### 查询索引库的settings信息

curl -XGET http://192.168.80.10:9200/user/_settings?pretty

```json
{
  "user" : {
    "settings" : {
      "index" : {
        "creation_date" : "1595406661327",
        "number_of_shards" : "1",
        "number_of_replicas" : "1",
        "uuid" : "ItJXeQraRqWCx5TQeyBlHQ",
        "version" : {
          "created" : "7070199"
        },
        "provided_name" : "user"
      }
    }
  }
}
```

**settings**修改索引库默认配置

例如：分片数量，副本数量

查看：`curl -XGET http://192.168.80.10:9200/user/_settings?pretty`

操作**不存在**索引：

```sh
curl -XPUT '192.168.80.10:9200/liuch/' -d'{"settings":{"number_of_shards":3,"number_of_replicas":0}}'
```

操作已**存在**索引：

```sh
curl -XPUT '192.168.80.10:9200/user/_settings' -d'{"index":{"number_of_replicas":1}}'
```

总结：就是，不存在索引时,可以指定副本和分片，如果已经存在,则只能修改副本。

在创建新的索引库时，可以指定索引分片的副本数。默认是1，这个很简单

## **2、ES中的mappings**

**ES的mapping如何用？什么时候需要手动，什么时候需要自动？**

**Mapping**,就是对索引库中索引的字段名称及其数据类型进行定义，类似于mysql中的表结构信息。不过es的mapping比数据库灵活很多，它可以动态识别字段。一般不需要指定mapping都可以，因为es会自动根据数据格式识别它的类型，**如果你**需要对某些字段添加特殊属性（如：定义使用其它分词器、是否分词、是否存储等），**就必须手动添加mapping**。

我们在es中添加索引数据时不需要指定数据类型，es中有自动影射机制，字符串映射为string，数字映射为long。通过mappings可以指定数据类型是否存储等属性。

###  查询索引库的mapping信息

`curl -XGET http://10.0.5.46/user/_mappings\?pretty`

```json
{
  "user" : {
    "mappings" : {
      "properties" : {
        "PassWord" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "UserName" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "created_at" : {
          "type" : "long"
        },
        "id" : {
          "type" : "long"
        },
        "status" : {
          "type" : "long"
        },
        "updated_at" : {
          "type" : "long"
        }
      }
    }
  }
}
```



### mappings修改字段相关属性

例如：字段类型，使用哪种分词工具啊等，如下：

注意：下面可以使用indexAnalyzer定义分词器，也可以使用index_analyzer定义分词器


操作不存在的索引

```sh
curl -XPUT '192.168.80.10:9200/user' -d'{"mappings":{"emp":{"properties":{"name":{"type":"string","analyzer": "ik_max_word"}}}}}'
```

操作已存在的索引

```sh
curl -XPOST http://192.168.80.10:9200/user/emp/_mapping -d'{"properties":{"name":{"type":"string","analyzer": "ik_max_word"}}}'
```


