# SVN 命令

1. 添加所有工作拷贝的未版本化文件，包括那些隐藏在深处的文件，可以使用svn add的--force递归到版本化的目录下
svn add * --force

2. svn 忽略文件
~/.subversion/config文件 搜索 global-ignores

3. svn log显示最新几行
svn log | head -n 10

4. 恢复本地修改
svn revert: 恢复原始未改变的工作副本文件 (恢复大部份的本地修改)
用法: revert PATH…

5. 解决冲突
svn resolved: 移除工作副本的目录或文件的“冲突”状态。
用法: resolved PATH…
注意: 本子命令不会依语法来解决冲突或是移除冲突标记；它只是移除冲突的
