# SVN 常用命令

1. 添加所有工作拷贝的未版本化文件，包括那些隐藏在深处的文件，可以使用svn add的--force递归到版本化的目录下
```sh
svn add * --force
```

2. svn 忽略文件
```sh
~/.subversion/config文件 搜索 global-ignores
```

3. svn log显示最新几行
```sh
svn log | head -n 10
```

4. svn 撤销本地修改
```sh
svn revert -R templates/default
```