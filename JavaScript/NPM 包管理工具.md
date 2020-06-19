# NPM 包管理工具

> npm 是 Node.js 的包管理工具，用来安装各种 Node.js 的扩展。

## NPM 镜像
淘宝 npm 地址： http://npm.taobao.org/
淘宝npm镜像使用方法:
* 临时使用
```sh
npm --registry https://registry.npm.taobao.org install express
```
* 持久使用
```sh
npm config set registry https://registry.npm.taobao.org
```
配置后可通过下面方式来验证是否成功
```sh
npm config get registry
# 或
npm info express
```

### cnpm使用
```sh
npm install -g cnpm --registry=https://registry.npm.taobao.org
# 使用
cnpm install express
```