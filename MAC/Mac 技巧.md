

# Mac 系统技巧

### MAC 终端走代理服务器

问题描述: MAC 终端，默认不走代理服务器；即浏览器已经可以翻墙，但是终端不行；

解决方案：直接设置终端的代理，本文 用的是 shadowSocksX；

打开终端，直接执行：(执行后，只对当前终端起作用；重启终端后，默认失效；)
```sh
export http_proxy=socks5://127.0.0.1:1080 # 配置http 代理访问
export https_proxy=socks5://127.0.0.1:1080 # 配置https 代理访问
export all_proxy=socks5://127.0.0.1:1080 # 配置http和https访问
unset http_proxy  # 取消http 代理访问
```



### MacOS 10.12安全与隐私没有允许任何来源的app选项解决办法

不少用户更新到macOS10.12之后 发现在系统偏好设置->安全性与隐私中已经去除了允许安装任何来源App的选项，没有这个选项估计好多不是从Mac App Store中下载的软件都不能正常安装，官方给出的解决办法是 control + 点按要打开的App文件即可，但是感觉还是不方便，查询了官方的支持Gatekeeper的原因，10.12默认是打开的，只需要关掉Gatekeeper就可以看到选项了。

具体执行方法是在终端执行一下方法
显示允许安装任何来源的App

```sh
sudo spctl --master-disable
```

隐藏允许安装任何来源的App

```sh
sudo spctl --master-enable
```