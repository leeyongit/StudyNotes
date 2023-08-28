# 用Pyinstaller 打包 Python 生成 Mac OS X 应用

安装

```sh
pip3 install pyinstaller
```

然后在 terminal 中 cd 到项目路径，
运行程序入口，也就是main函数所在文件 main.py
在项目所在工作路径输入命令：

```sh
sudo pyinstaller --windowed --onefile --clean --noconfirm main.py
sudo pyinstaller --clean --noconfirm --windowed --onefile main.spec
```

sudo 获取权限，可能要求输入密码
如果想要打出来的包有图标第一条命了替换：

```sh
pyinstaller --windowed --onefile --icon=sat_tool_icon.icns --clean --noconfirm main.py
```



## Pyinstaller基本使用方法

Pyinstaller可以通过简单的命令进行python代码的打包工作，其基本的命令为：

```sh
pyinstaller -option xxx.py
```

options的详情可参考官方帮助文档https://pyinstaller.readthedocs.io/en/stable/usage.html

这边只介绍用到的option：

> -d 生成一个文件目录包含可执行文件和相关动态链接库和资源文件等；
>
> -f 仅生成一个可执行文件

| -D, --onedir  | Create a one-folder bundle containing an executable (default) |
| ------------- | ------------------------------------------------------------ |
| -F, --onefile | Create a one-file bundled executable.                        |

对于打包结果较大的项目，选用-d生成目录相比单可执行文件的打包方式，执行速度更快，但包含更加多的文件。本文的例子选中-D方式打包。