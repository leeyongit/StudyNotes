## yarn 安装与更换源

### 安装

https://classic.yarnpkg.com/zh-Hans/docs/install

##### 查询与更换源

```csharp
yarn config get registry

yarn config set registry https://registry.npm.taobao.org/
```

##### 使用方法：安装依赖

下载所有依赖：

```mipsasm
yarn install
```

添加一个依赖：

```csharp
yarn add axios
```

### npm更换源

npm也可以更换源，不过实测，更换源之后，搜索命令`npm search`无法使用，需要使用对应的网页搜索，要使用搜索，删除registry即可。

```csharp
npm config get registry

npm config set registry https://registry.npm.taobao.org/
```

##### 恢复官方源

你可以这样设置官方源，从而恢复，不过，搜索依然无法使用：

```bash
yarn config set registry https://registry.yarnpkg.com
npm config set registry https://registry.npmjs.org
```

要恢复搜索功能，请使用删除：

```cpp
yarn config delete registry
npm config delete registry
```

##### finaly

yarn无法搜索，npm换源后无法搜索，所以最终采用的方法是：
yarn更换源为国内镜像，npm保持搜索功能！

##### 附录

淘宝npm镜像

* 搜索地址：[http://npm.taobao.org](http://npm.taobao.org/)
* registry地址：[http://registry.npm.taobao.org](http://registry.npm.taobao.org/)

cnpmjs镜像

* 搜索地址：[http://cnpmjs.org](http://cnpmjs.org/)
* registry地址：[http://r.cnpmjs.org](http://r.cnpmjs.org/)

### 问题记录

###### npm/yarn报错there appears to be trouble with your network connection. retrying

1. **添加超时时间**

- `yarn add <yourPackage> --network-timeout 100000`
- `yarn install --network-timeout 100000`

2. **去掉代理`proxy`**

```bash
npm config rm proxy
npm config rm https-proxy
```

