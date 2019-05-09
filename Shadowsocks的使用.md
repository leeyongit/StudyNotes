Shadowsocks的使用
================
## 服务器端：
一般 CentOS 系统默认自带 python(系统要用，比如 yum 工具就是 python 写的)，但是经验看一般都不带 pip 工具，需要手动安装。
1. 安装扩展源EPEL
# CentOS yum源 中默认没有 pip，需要安装 扩展源EPEL
sudo yum -y install epel-release

2. 安装 pip
sudo yum -y install python-pip

3. 检查是否安装成功
pip -V

4. 然后执行安装Shadowsocks。
```sh
pip install shadowsocks
```

5. 然后使用sudo ssserver -p 端口号 -k 密码 -m 加密方式 start启动Shadowsocks。
```sh
sudo ssserver -p 8586 -k fshd2019 -m rc4-md5 -d start
```
端口号是一个数字，比如5678。
密码随意。
加密方式有多种选择，我写的是rc4-md5。
参考https://github.com/shadowsocks/shadowsocks

## 客户端：
点击http://sourceforge.net/projects/shadowsocksgui/，即可下载客户端。
安装完毕后，打开ShadowsocksX应用，配置一个服务器。
地址填写你的服务器地址，端口填写你上面命令的端口号，加密方式选择上面填写的加密方式，创建即可。
如果浏览器中使用了其他的代理插件，先关掉，或者将其配置切换到使用系统代理。
客户端在顶部会出现一个纸飞机的图标，里面有个编辑自动模式的PAC，在这里添加的地址，都会通过Shadowsocks来访问。填写的规则直接参考里面已有的地址即可。
如果使用全局模式，则不用考虑上一步。