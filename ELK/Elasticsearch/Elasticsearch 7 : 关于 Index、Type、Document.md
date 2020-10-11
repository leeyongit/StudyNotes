# Elasticsearch 7 : 关于 Index、Type、Document

### 含义

- Index：索引。复数是 Indices。
- Type：类型。
- Document：文档。

文档是 JSON 类型的。

### 与 MySQL 类比

可以将 ES 中的这三个概念和 MySQL 类比：

- Index 对应 MySQL 中的 Database；
- ~~Type 对应 MySQL 中的 Table；~~
- Document 对应 MySQL 中表的记录。

一个MySQL实例中可以创建多个 Database，一个Database中可以创建多个Table。

### 从 ES 7.0 开始，Type 被废弃

在 7.0 以及之后的版本中 Type 被废弃了。一个 index 中只有一个默认的 type，即 `_doc`。

ES 的Type 被废弃后，库表合一，Index 既可以被认为对应 MySQL 的 Database，也可以认为对应 table。

也可以这样理解：

- ES 实例：对应 MySQL 实例中的一个 Database。
- Index 对应 MySQL 中的 Table 。
- Document 对应 MySQL 中表的记录。