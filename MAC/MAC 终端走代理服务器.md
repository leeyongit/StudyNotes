MAC 终端走代理服务器
---
问题描述: MAC 终端，默认不走代理服务器；即浏览器已经可以翻墙，但是终端不行；

解决方案：直接设置终端的代理，本文 用的是 shadowSocksX；

打开终端，直接执行：(执行后，只对当前终端起作用；重启终端后，默认失效；)
```sh
export http_proxy=socks5://127.0.0.1:1080 # 配置http 代理访问
export https_proxy=socks5://127.0.0.1:1080 # 配置https 代理访问
export all_proxy=socks5://127.0.0.1:1080 # 配置http和https访问
unset http_proxy  # 取消http 代理访问
```

/usr/local/php5-7.2.9-20180821-074958/lib/php/extensions/no-debug-non-zts-20170718/redis.so