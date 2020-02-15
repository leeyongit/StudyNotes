MacOS 10.12安全与隐私没有允许任何来源的app选项解决办法
---

不少用户更新到macOS10.12之后 发现在系统偏好设置->安全性与隐私中已经去除了允许安装任何来源App的选项，没有这个选项估计好多不是从Mac App Store中下载的软件都不能正常安装，官方给出的解决办法是 control + 点按要打开的App文件即可，但是感觉还是不方便，查询了官方的支持Gatekeeper的原因，10.12默认是打开的，只需要关掉Gatekeeper就可以看到选项了。
具体执行方法是在终端执行一下方法
显示允许安装任何来源的App
sudo spctl --master-disable

隐藏允许安装任何来源的App
sudo spctl --master-enable

