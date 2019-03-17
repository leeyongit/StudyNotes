# Elasticsearch

## Docker部署ElasticHD
> Elasticsearch 可视化DashBoard, 支持Es监控、实时搜索，Index template快捷替换修改，索引列表信息查看， SQL converts to DSL等
```sh
docker run -p 9200:9200 -d --name elasticsearch elasticsearch
docker run -p 9800:9800 -d --link elasticsearch:demo containerize/elastichd
```