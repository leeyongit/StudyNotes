### all shards failed [type=search_phase_execution_exception]

使用elasticsearch出现标题的异常，出现原因:

* 当使用到term 查询的时候，由于是精准匹配，所以查询的关键字在es上的类型，必须是keyword而不能是text，比如你的搜索条件是 ”name”:”蔡虚坤”,那么该name 字段的es类型得是keyword，而不能是text。

* 在es中，只有keyword类型的字符串可以使用AggregationBuilders.terms("aggs-class")来分组聚合，想要分组查询，指定根据分组字段的keyword属性就可以了。

