# 变量相关
```sh
type(variable).__name__   #  获取变量类型(字符串表示)
isinstance(variable, str) # 判断 variable 是否是字符串, 拓展: 判断变量是否是某个类的实例
isinstance(var, (str, int)) # 判断 variable 是否是字符串或者整数
```
# 字符串相关
```sh
 print("i= %d, j=%d" % (i, j))  # `printf` 的 python 实现
```
# 异常相关
```sh
raise SomeError(message) #  抛出异常
```
# 类与对象
```sh
hasattr(o, name) # 判断对象 o 里面是否有名字为 name 的属性
```
# yield
```sh
def iterator():
    i = 1
    j = -1
    while i < 10 and j > -10:
        i = i + 1
        j = j - 1
        yield i, j

for i, j in iterator():
    print("i= %d, j= %d" % (i, j))
```
# 文件操作

# 遍历目录
```sh
import os
for root, dirs, files in os.walk(os.path.abspath(os.getcwd() + '/..')):
    # 如若需要完整路径则需要加上 root 前缀
    if ".idea" in dirs:
        dirs.remove('.idea')
    pass
```

# Scrapy框架

## MacOX 安装 requests
```sh
sudo pip3 install requests
sudo pip3 install PyMySQL
scrapy crawl name
pip3 list
```

## 新建项目
```sh
scrapy startproject mySpider
```
## 制作爬虫
```sh
scrapy genspider itcast "itcast.cn" # 将在mySpider/spider目录下创建一个名为itcast的爬虫，并指定爬取域的范围
```
## 运行爬虫
```sh
scrapy crawl itcast
```

## 美团足疗保健api
https://apimobile.meituan.com/group/v4/poi/pcsearch/1?uuid=ce410796c00241daae32.1547528581.1.0.0&userid=-1&limit=32&offset=64&cateId=-1&q=%E8%B6%B3%E7%96%97


# Python打包方法——Pyinstaller
## 安装Pyinstaller
使用pip命令安装：
```sh
pip install PyInstaller
pyinstaller -F myfile.py
```

# Numpy 和 Pandas 安装
pip3 install numpy
pip3 install pandas


