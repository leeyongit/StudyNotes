# Ionic

1. 安装npm 、node.js
2. 安装ionic和cordova

```sh
npm install -g cnpm --registry=https://registry.npm.taobao.org
sudo cnpm install -g cordova ionic
ionic info
```

## 安装（失败的话 Mac 尝试使用 sudo，Windows 尝试管理员身份运行 cmd）
```sh
$ npm install -g cordova ionic
# 安装后可以验证一下 ionic cli 版本
$ ionic -version
3.5.0
```

## 创建应用
cd 到要创建项目的目录，输入以下内容创建ionic项目
ionic3Demo 是项目名，tabs是模板（默认是tabs，其他还有blank的单页等）
```sh
ionic start ionic3Demo tabs
```
## 安装依赖
会生成一个 node_modules 文件夹，并在里面安装 package.json 上写下的文件
```sh
cd ionic3Demo/
npm install
```
## 在浏览器中运行项目
```sh
$ ionic serve
```

## 如果需要在手机运行，则执行以下命令

```sh
# 添加iOS项目
ionic cordova platform add ios # 把src里的内容同步到ios项目中(src后面会讲到)
ionic cordova build ios # 运行iOS项目,相当于在Xcode里面按Command+R
ionic cordova emulate ios
```
## 安卓的话同理，把ios替换为android即可
```sh
ionic cordova platform add android
ionic cordova build android
ionic cordova emulate android
```

