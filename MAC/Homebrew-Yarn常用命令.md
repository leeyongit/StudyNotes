# Homebrew/Yarn常用命令

## Homebrew常用命令

```bash
brew search git        // 搜索软件包
brew install git        // 安装软件包
brew unistall git        // 卸载软件包
brew list        // 显示已经安装的所有软件包
brew update        // 同步远程最新更新情况，对本机已经安装并有更新的软件用*标明
brew outdated        // 查看本机需要更新的软件包
brew upgrade        // 更新所有软件包
brew upgrade git        // 更新单个软件包
brew cleanup -n        // 查看可以清理的旧版本包
brew cleanup        // 清理所有包的旧版本的缓存
brew cleanup git        // 清理指定包的旧版本
```

## Yarn常用命令

```sh
yarn init        // 项目初始化,等同于npm的npm init
yarn / $ yarn install        // 安装package.json中的依赖包,等同于npm install
yarn add vue        // 添加并安装依赖包,等同于npm install vue(现在无需加--save,已经成为默认行为)
yarn global add vue        // 全局安装包
yarn remove vue        // 删除依赖包,等同于npm uninstall vue
yarn upgrade        // 升级package.json指定的所有依赖包(在package.json指定的版本范围内)
yarn upgrade --latest        //升级package.json指定的所有依赖包,但忽略package.json指定的版本范围,同时package.json中指定的版本将被重写
yarn outdated        // 列出包的所有依赖项的版本信息。此信息包括当前安装的版本、基于语义版本所需的版本和最新的可用版本
yarn run        // 列出包里所有可运行的脚本
yarn run dev        // 运行package.json中scripts定义的脚本命令,等同于npm run
```

## 把Yarn的下载源设置为淘宝镜像

```sh
yarn config get registry    //查看当前下载源,初始为https://registry.yarnpkg.com
yarn config set registry https://registry.npm.taobao.org        //更改为淘宝
```