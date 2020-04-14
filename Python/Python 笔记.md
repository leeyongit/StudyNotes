# Python 笔记

## 镜像
- `http://pypi.douban.com/` 豆瓣
- `http://pypi.hustunique.com/` 华中理工大学
- `http://pypi.tuna.tsinghua.edu.cn/simple/` 清华大学
- `http://mirrors.aliyun.com.pypi/simple/` 阿里云

使用镜像来安装包指
```python
pip install pillow -i http://pypi.tuna.tsinghua.edu.cn/simple/ --trusted-host pypi.tuna.tsinghua.edu.cn
```
--trusted-host和之后的是为了防止出现不信任的报错更改超时时间
```python
pip --default-timeout=500 install -U pillow
```
超时时间默认为100，改大点就好（这里我改成了500）
pip3 --default-timeout=500 install mysql-connector -i http://pypi.tuna.tsinghua.edu.cn/simple/ --trusted-host pypi.tuna.tsinghua.edu.cn
## 环境配置
### venv虚拟环境安装配置
```sh
sudo pip3 install virtualenv
virtualenv venv
. venv/bin/activate
```

## Python 查看已经安装的软件包及版本的方法
```sh
pip freeze
pip list
pydoc modules
```

## Scrapy框架
### MacOX 安装 requests
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

## Numpy
```sh
pip3 install numpy
```

## Pandas
```sh
pip3 install pandas
```

## Python打包方法——Pyinstaller
### 安装Pyinstaller
使用pip命令安装：
```sh
pip install PyInstaller
pyinstaller -F myfile.py
```