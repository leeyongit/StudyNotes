Shell
---
### 分析nginx中的access.log 查找出 某个时间段内ip地址 请求次数最高的
```sh
awk '{print $1}' access.log |sort|uniq -c|sort -k1nr|head -n 1
```
awk默认按空格进行切分，$1为ip位置
sort 参数：
    -n: numeric-sort，根据字符串数值进行排序
    -r: reverse，反向输出排序结果
    -k N: key，以第N列进行排序
uniq -c：可以统计重复行出现的次数

### 二、 2个很大的文件、查找相同的内容

比较两个文件： comm file1 file2
只显示file1独有的行：comm -2 -3 file1 file2
只显示file2独有的行：comm -1 -3 file1 file2
**只显示两者重复的行：comm -1 -2 file1 file2**
只显示两者不重复的行：comm -3 file1 file2 | sed 's/^\t//' 后面的sed是将以\t开头的\t去掉