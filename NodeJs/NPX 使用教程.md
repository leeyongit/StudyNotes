# NPX 使用
npm 从5.2版开始，增加了 npx 命令。

Node 自带 npm 模块，所以可以直接使用 npx 命令。万一不能用，就要手动安装一下。

```sh
npm install -g npx
```
```sh
# 使用nrm工具切换淘宝源
npx nrm use taobao

# 如果之后需要切换回官方源可使用
npx nrm use npm
```
## 调用项目安装的模块
npx 想要解决的主要问题，就是调用项目内部安装的模块。比如，项目内部安装了测试工具 Mocha。

```sh
> npm install -D mocha
```
一般来说，调用 Mocha ，只能在项目脚本和 package.json 的scripts字段里面， 如果想在命令行下调用，必须像下面这样。

```sh
# 项目的根目录下执行
> node-modules/.bin/mocha --version
```
npx 就是想解决这个问题，让项目内部安装的模块用起来更方便，只要像下面这样调用就行了。
```sh
> npx mocha --version
```

## 避免全局安装模块
除了调用项目内部模块，npx还能避免全局安装的模块。比如，create-react-app这个模块是全局安装，npx 可以运行它，而且不进行全局安装。
```js
> npx create-react-app my-react-app
```
上面代码运行时，npx 将create-react-app下载到一个临时目录，使用以后再删除。所以，以后再次执行上面的命令，会重新下载create-react-app。

参考资料：
[npx 使用教程](http://www.ruanyifeng.com/blog/2019/02/npx.html)