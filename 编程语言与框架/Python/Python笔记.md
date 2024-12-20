
### 使用镜像
- `http://pypi.douban.com/` 豆瓣
- `http://pypi.hustunique.com/` 华中理工大学
- `http://pypi.tuna.tsinghua.edu.cn/simple/` 清华大学
- `http://mirrors.aliyun.com.pypi/simple/` 阿里云

使用镜像来安装包指
```python
pip3 install pillow -i http://pypi.tuna.tsinghua.edu.cn/simple/ --trusted-host pypi.tuna.tsinghua.edu.cn
```
--trusted-host和之后的是为了防止出现不信任的报错更改超时时间
```python
pip3 --default-timeout=500 install -U pillow
```
超时时间默认为100，改大点就好（这里我改成了500）

### 使用pipreqs一键安装Python项目所有依赖包

```python
# 安装pipreqs工具
pip install pipreqs 
# 在项目根目录下执行  生成所需依赖包名、版本
pipreqs ./
# 安装所需依赖
pip install -r requirements.txt
```



### MAC安装chromedriver

选择与浏览器版本一致的版本下载

http://npm.taobao.org/mirrors/chromedriver/

```sh
cp chromedriver /usr/local/bin
```



### 解决:pip is configured with locations that require TLS/SSL

打开终端，输入

```
mkdir ~/.pip    
cd ~/.pip
```

创建pip.conf文件:

```
touch pip.conf
```

复制粘贴以下配置到pip.conf文件中：

```
[global]
index-url = http://mirrors.aliyun.com/pypi/simple/ 

[install]
trusted-host=mirrors.aliyun.com
```

