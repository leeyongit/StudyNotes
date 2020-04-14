# Scrapy框架

## MacOX 安装 requests
```sh
sudo pip3 install requests
sudo pip3 install PyMySQL
scrapy crawl name
pip3 list
```
新建项目
```sh
scrapy startproject mySpider
```
制作爬虫
spider目录下创建一个名为itcast的爬虫，并指定爬取域的范围
```sh
scrapy genspider itcast "itcast.cn" # 将在mySpider/
```
运行爬虫
```sh
scrapy crawl itcast
```