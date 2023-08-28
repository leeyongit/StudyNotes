# php扩展之pcntl

pcntl_fork — 在当前进程当前位置产生分支（子进程）。译注：fork是创建了一个子进程，父进程和子进程 都从fork的位置开始向下继续执行，不同的是父进程执行过程中，得到的fork返回值为子进程 号，而子进程得到的是0。

pcntl_fork()函数创建一个子进程，这个子进程仅PID（进程号） 和PPID（父进程号）与其父进程不同

* 具体编译方法

```bash
# 下载源码包
wget http://cn.php.net/distributions/php-7.3.9.tar.gz

# 解压
tar zxvf php-7.3.9.tar.gz

# 进入文件执行编译
cd php-7.3.9/ext/pcntl
/Applications/MAMP/bin/php/php7.3.9/bin/phpize
./configure --with-php-config=/Applications/MAMP/bin/php/php7.3.9/bin/php-config
make

# 拷贝编译.so文件到MAMP extensions目录(具体的文件夹看自己的目录哦)
cp modules/pcntl.so /Applications/MAMP/bin/php/php7.3.9/lib/php/extensions/no-debug-non-zts-20180731/

# 编辑php.ini引入扩展
echo "extension=pcntl.so" >> /Applications/MAMP/bin/php/php7.3.9/conf/php.ini
```

* 检查是否安装成功

  ```sh
  php --ri pcntl
  ```