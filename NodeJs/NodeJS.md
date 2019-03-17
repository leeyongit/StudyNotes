# npm太慢， 淘宝npm镜像使用方法
淘宝 npm 地址： http://npm.taobao.org/
## 临时使用 
```sh
npm --registry https://registry.npm.taobao.org install express
```
## 持久使用
```sh
npm config set registry https://registry.npm.taobao.org
```
配置后可通过下面方式来验证是否成功
```sh
npm config get registry
# 或
npm info express
```
## 通过cnpm使用
```sh
npm install -g cnpm --registry=https://registry.npm.taobao.org
# 使用
cnpm install express
```

## Laravel Mix的又一个坑：系统找不到指定的路径。

```sh
npm install
npm run dev
npm install webpack --save
```