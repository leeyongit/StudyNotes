Java JDK在linux系统有两个版本，一个开源版本Openjdk，还有一个oracle官方版本jdk，oracle JDK既可以通过添加ppa源命令行安装，也可以去官网下载jdk压缩包安装。

## 安装openjdk
1、更新软件包列表：

```bash
sudo apt-get update
```

2、安装openjdk-8-jdk：

```bash
sudo apt-get install openjdk-8-jdk
```

3、查看java版本，看看是否安装成功：

```bash
java -version
```

