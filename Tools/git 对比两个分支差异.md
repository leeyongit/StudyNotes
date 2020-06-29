# git 对比两个分支差异

1. 显示出branch1和branch2中差异的部分
```
git diff branch1 branch2 --stat
```
2. 显示指定文件的详细差异
```
git diff branch1 branch2 具体文件路径
```
3. 显示出所有有差异的文件的详细差异
```
git diff branch1 branch2
```
4. 查看branch1分支有，而branch2中没有的log
```
git log branch1 ^branch2
```
5. 查看branch2中比branch1中多提交了哪些内容
```
git log branch1..branch2
```
注意，列出来的是两个点后边（此处即dev）多提交的内容。

6. 不知道谁提交的多谁提交的少，单纯想知道有是吗不一样
```
git log branch1...branch2
```
7. 在上述情况下，在显示出没个提交是在哪个分支上
```
git log --lefg-right branch1...branch2
```
注意 commit 后面的箭头，根据我们在 –left-right branch1…branch2 的顺序，左箭头 < 表示是 branch1 的，右箭头 > 表示是branch2的。

8. Git合并单个文件和[y,n,q,a,d,/,K,j,J,g,e,?]
将A分支的a文件合并到B分支的a文件上。可以通过以下方式合并
```
git checkout B
git checkout --patch A a
```
[y,n,q,a,d,/,K,j,J,g,e,?]
y - 存储这个hunk
n - 不存储这个hunk
q - 离开，不存储这个hunk和其他hunk
a - 存储这个hunk和这个文件后面的hunk
d - 不存储这个hunk和这个文件后面的hunk
g - 选择一个hunk
/ - 通过正则查找hunk
j - 不确定是否存储这个hunk，看下一个不确定的hunk
J - 不确定是否存储这个hunk，看下一个hunk
k - 不确定是否存储这个hunk，看上一个不确定的hunk
K -不确定是否存储这个hunk，看上一个hunk
s - 把当前的hunk分成更小的hunks
e - 手动编辑当前的hunk
? - 输出帮助信息
