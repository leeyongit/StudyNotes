# Git 笔记

### 全局设置用户信息

```sh
git config --global user.name "李永"
git config --global user.email "leeyongit@163.com"
```

### 用命令行设置一个仓库

```sh
echo "# go-files" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:leeyongit/go-files.git
git push -u origin master
```

### 用命令行推送已经存在的仓库

```sh
git remote add origin git@github.com:leeyongit/go-files.git
git push -u origin master
```

## Git常用命令整理

### 远程建立了dev分支本地获取对应分支
```sh
git checkout --track origin/dev
```

### 查看文件的修改记录

```sh
git log fileName
```
如果文件修改记录太多，则使用
```sh
git log -number fileName
```
显示从最近到最远的提交日志
```sh
git log --pretty=oneline
```

### git reset 回退到指定版本

git reset的作用是还原Index的状态或修改本地分支HEAD的位置。
```sh
git reset ** fileName
```

* git reset -mixed：此为默认方式，不带任何参数的git reset，这种方式，它回退到某个版本，只保留源码，回退commit和index信息
* git reset -soft:回退到某个版本，只回退了commit的信息，不会恢复到index file一级。如果还要提交，直接commit即可
* git reset -hard 彻底回退到某个版本，本地的源码也会变成为上一个版本的内容

#### 回退到上一版本
```sh
git reset --hard HEAD^
git reset HEAD^
```
#### 指定回到某一个版本
```sh
git reset --hard commit_id
git reset commit_id
```

#### 回退某个文件的版本到上一个版本  
```sh
git reset HEAD^ filename
```

#### 向前回退到第3个版本  
```sh
git reset –soft HEAD~3  
```

#### 将本地的状态回退到和远程的一样
```sh  
git reset –hard origin/master  
```

### git revert

> 假如某些旧提交我们不想要了，而又不愿通过自己修改代码重新提交的方式来完成，这个时候我们就可以把这项工作交给revert命令。
> Git Revert原理：根据你要回退的提交所做的改动做相反的改动，然后重新提交代码，使代码达到没有这些旧提交所能达到的状态。

### 回退到上一次提交的状态，按照某一次的commit完全反向的进行一次commit.(代码回滚到上个版本，并提交git)
```sh
git revert HEAD
```


#### 取消已经暂存的文件。即，撤销先前"git add"的操作
```sh
git reset HEAD <file>...
git reset HEAD -filename # 如果是撤销某个文件或文件夹
git reset HEAD . # 如果是撤销所有的已经add的文件
```

### 查看命令历史
```sh
git reflog
```

### Git鼓励大量使用分支：
```sh
git branch # 查看分支
git branch <name> # 创建分支
git checkout <name> # 切换分支
git checkout -b <name> # 创建+切换分支
git merge <name> # 合并某分支到当前分支
git branch -d <name> # 删除分支
```

### git 取消修改，恢复版本 
取消对文件的修改。还原到最近的版本，废弃本地做的修改。
```sh
git checkout -- <file>
```

### 修改最后一次提交。用于修改上一次的提交信息，或漏提交文件等情况。
```sh
git commit --amend
```





