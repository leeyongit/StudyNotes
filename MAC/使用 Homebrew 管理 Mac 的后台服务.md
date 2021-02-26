> 摘译自 [robots.thoughtbot.com](http://robots.thoughtbot.com/starting-and-stopping-background-services-with-homebrew)

`launchctl` 命令加载，卸载开机自动运行的服务，在 OS X 中，服务本身存储在 `.plist` 文件中（即 property list），这些文件的位置一般在 `~/Library/LaunchAgents` 或 `/Library/LaunchAgents`。可以使用 `launchctl load $PATH_TO_LIST` 和 `unload them with launchctl unload $PATH_TO_LIST` 命令来加载/卸载他们。加载就是允许这个程序开机执行，卸载反之。

如果你使用 `Homebrew` 安装过 `mysql` 那么下面的安装后提示你可能比较熟悉

 ```
To have launchd start mysql at login:
    ln -sfv /usr/local/opt/mysql/*.plist ~/Library/LaunchAgents
Then to load mysql now:
    launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
Or, if you don't want/need launchctl, you can just run:
    mysql.server start
 ```

如果按上面的说明操作的话，未免太麻烦了，而且也很难记住 plist 的位置。还好 Homebrew 提供了一个易用的接口来管理 plist，然后你就不用再纠结什么 `ln`，`launchctl`，和 plist 的位置了。

####brew services
首先安装 `brew services` 命令
 ```
 brew tap gapple/services
 ```
下面举个例子
 ```
 $ brew services start mysql
==> Successfully started `mysql` (label: homebrew.mxcl.mysql)
 ```

在后台，`brew services start` 其实执行了最上面的安装后消息里面提到的所有命令，比如首先运行 `ln -sfv ...`，然后 `launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist` 。

假设突然 MySQL 出毛病了，我们要重启一下，那么执行下面的命令就行了
 ```
brew services restart mysql
Stopping `mysql`... (might take a while)
==> Successfully stopped `mysql` (label: homebrew.mxcl.mysql)
==> Successfully started `mysql` (label: homebrew.mxcl.mysql)
 ```

想看所有的已启用的服务的话：
 ```
 $ brew services list
redis      started      442 /Users/gabe/Library/LaunchAgents/homebrew.mxcl.redis.plist
postgresql started      443 /Users/gabe/Library/LaunchAgents/homebrew.mxcl.postgresql.plist
mongodb    started      444 /Users/gabe/Library/LaunchAgents/homebrew.mxcl.mongodb.plist
memcached  started      445 /Users/gabe/Library/LaunchAgents/homebrew.mxcl.memcached.plist
mysql      started    87538 /Users/gabe/Library/LaunchAgents/homebrew.mxcl.mysql.plist
 ```

要注意的是，这里不止显示通过 `brew services` 加载的服务，也包含 `launchctl load` 加载的。

如果你卸载了 MySQL 但是 `Homebrew` 没把 plist 文件删除的话，你可以
 ```
$ brew services cleanup
Removing unused plist /Users/gabe/Library/LaunchAgents/homebrew.mxcl.mysql.plist
 ```


> 最后一提，这玩意路径不一样，会直接复制到 LaunchAgents 目录，结果就是两个一样的启动项了，系统 log 里会很烦人的。。