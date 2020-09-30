# Git 笔记

## 基本概念
我们先来理解下Git 工作区、暂存区和版本库概念

- **工作区：**就是你在电脑里能看到的目录。
- **暂存区：**英文叫stage, 或index。一般存放在 ".git目录下" 下的index文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。
- **版本库：**工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。

![img](https://www.runoob.com/wp-content/uploads/2015/02/1352126739_7909.jpg)

全局设置用户信息

```sh
git config --global user.name "李永"
git config --global user.email "leeyongit@163.com"
```

用命令行创建一个新仓库

```sh
echo "# go-files" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:leeyongit/go-files.git
git push -u origin master
```

用命令行推送已经存在的仓库

```sh
git remote add origin git@github.com:leeyongit/go-files.git
git push -u origin master
```
## 新增 commit
```
// 添加文件到缓存区，然后提交到本地仓库
git add files
git commit -m '提交备注'
```
## 撤销 commit
```
// 会将提交记录回滚，代码不回滚
git reset b14bb52

// 会将提交记录和代码全部回滚
git reset --hard b14bb52

// 将部分代码文件回滚
git checkout -- files
```
## 合并 commit
合并 commit，本质上合并两份不同状态下的代码。
```
// Git 提供了两种合并 commit 的方式
git merge master
git rebase master
```
那么 git rebase 和 git merge 到底有什么区别呢？
merge是两个分支处理冲突后，新增一个 commit 追加到master上。
rebase是将someFeature分支上的commit记录追加到主分支上，值得注意的是，这个时候他的commit其实已经发生变化。

## Git常用命令整理
```sh
git reflog                      # 查看命令历史
```
### Git 日志
```sh
git log fileName                # 查看文件的修改记录
git log -number fileName        # 如果文件修改记录太多，则使用
git log --pretty=oneline        # 显示从最近到最远的提交日志
```

### git reset 回退到指定版本

git reset的作用是还原Index的状态或修改本地分支HEAD的位置。
```sh
git reset ** fileName
```
* git reset --mixed：此为默认方式，不带任何参数的git reset，这种方式，它回退到某个版本，只保留源码，回退commit和index信息
* git reset --soft:回退到某个版本，只回退了commit的信息，不会恢复到index file一级。如果还要提交，直接commit即可
* git reset --hard 彻底回退到某个版本，本地的源码也会变成为上一个版本的内容

回退版本
```sh
git reset --hard HEAD^          // 回退到上一版本
git reset --hard HEAD^ filename // 回退某个文件的版本到上一个版本
git reset --hard commit_id      // 指定回到某一个版本
git reset --hard origin/master // 本地的状态回退到和远程的一样
```

取消已经暂存的文件。即，撤销先前"git add"的操作

```sh
git reset HEAD <file>...
git reset HEAD -filename # 如果是撤销某个文件或文件夹
git reset HEAD . # 如果是撤销所有的已经add的文件
```
取消对文件的修改。还原到最近的版本，废弃本地做的修改。
```sh
git checkout -- <file>
```

### git revert

> 假如某些旧提交我们不想要了，而又不愿通过自己修改代码重新提交的方式来完成，这个时候我们就可以把这项工作交给revert命令。

> Git Revert原理：根据你要回退的提交所做的改动做相反的改动，然后重新提交代码，使代码达到没有这些旧提交所能达到的状态。

回退到上一次提交的状态，按照某一次的commit完全反向的进行一次commit.(代码回滚到上个版本，并提交git)

```sh
git revert HEAD
```



### Git分支
```sh
git branch # 查看分支
git branch <name> # 创建分支
git branch -m 原名 新名 # 分支改名
git branch -d <name> # 删除分支
git checkout <name> # 切换分支
git checkout -b <name> # 创建+切换分支
git checkout --track origin/dev # 远程建立了dev分支本地获取对应分支
git merge <name> # 合并某分支到当前分支
```

## 修改最后一次提交
用于修改上一次的提交信息，或漏提交文件等情况。

```sh
git commit --amend
```

git 解决每次更新代码都要输入用户名密码的解决方案

```sh
git config --global credential.helper store
git pull /git push # (第一次输入，后续就不用再次数据)
```

