Gradle是用于构建，自动化和交付软件的通用工具。 它主要用于Java，C ++和Swift项目。

Gradle结合了Ant和[ Maven ](https://www.myfreax.com/how-to-install-apache-maven-on-ubuntu-20-04/)的最佳功能。 与之前的版本使用XML编写脚本不同，Gradle使用[ Groovy ](http://groovy-lang.org/)（一种用于Java平台的动态，面向对象的编程语言）来定义项目和构建脚本。

本指南介绍了如何在Ubuntu 20.04上安装Gradle。 我们将从其官方网站下载最新版本的Gradle。

## 先决条件

假定您以root用户或具有sudo权限的[用户身份登录](https://www.myfreax.com/how-to-create-a-sudo-user-on-ubuntu/)。

## 安装OpenJDK

Gradle要求在计算机上安装Java SE 8或更高版本。

输入以下命令以[安装OpenJDK 11 ](https://www.myfreax.com/install-java-on-ubuntu-20-04/)：

```bash
sudo apt update
sudo apt install openjdk-11-jdk
```

Copy

通过打印[ Java版本](https://www.myfreax.com/how-to-check-java-version/)来验证Java安装：

```bash
java -version
```

Copy

输出应如下所示：

```bash
openjdk version "11.0.7" 2020-04-14
OpenJDK Runtime Environment (build 11.0.7+10-post-Ubuntu-3ubuntu1)
OpenJDK 64-Bit Server VM (build 11.0.7+10-post-Ubuntu-3ubuntu1, mixed mode, sharing)
```

Copy

## 下载Gradle

在撰写本文时，Gradle的最新版本是`6.5.1`。 在继续下一步之前，请查看[发行版页面](https://gradle.org/releases/)，查看是否有较新的版本。

使用以下[ `wget` ](https://www.myfreax.com/wget-command-examples/)命令在`/tmp`目录中下载Gradle仅二进制zip文件：

```bash
VERSION=6.5.1
wget https://services.gradle.org/distributions/gradle-${VERSION}-bin.zip -P /tmp
```

Copy

下载完成后，[将文件](https://www.myfreax.com/how-to-unzip-files-in-linux/)解压缩到`/opt/gradle`目录中：

```bash
sudo unzip -d /opt/gradle /tmp/gradle-${VERSION}-bin.zip
```

Copy

如果出现错误消息sudo: unzip: command not found，请使用`sudo apt install unzip`安装解压缩软件包。

Gradle会定期更新安全补丁和新功能。 为了更好地控制版本和更新，我们将[创建一个名为`latest`的符号链接](https://www.myfreax.com/how-to-create-symbolic-links-in-linux-using-the-ln-command/)，该链接指向Gradle安装目录：

```bash
sudo ln -s /opt/gradle/gradle-${VERSION} /opt/gradle/latest
```

Copy

稍后在升级Gradle时，请解压缩较新的版本并更改符号链接以指向它。

## 设置环境变量

我们需要将Gradle bin目录添加到系统`PATH`环境变量中。 为此，请打开[文本编辑器](https://www.myfreax.com/how-to-use-nano-text-editor/)，然后在`/etc/profile.d/`目录中创建一个名为`gradle.sh`的新文件。

```bash
sudo nano /etc/profile.d/gradle.sh
```

Copy

粘贴以下配置：

/etc/profile.d/gradle.sh

```bash
export GRADLE_HOME=/opt/gradle/latest
export PATH=${GRADLE_HOME}/bin:${PATH}
```

Copy

保存并关闭文件。 该脚本将在shell启动时提供，[使脚本可执行](https://www.myfreax.com/chmod-command-in-linux/)：

```bash
sudo chmod +x /etc/profile.d/gradle.sh
```

Copy

使用[ `source` ](https://www.myfreax.com/bash-source-command/)命令在当前Shell会话中加载环境变量：

```bash
source /etc/profile.d/gradle.sh
```

Copy

## 验证Gradle安装

要验证Gradle是否正确安装，请使用`gradle -v`命令，该命令将显示Gradle版本：

```bash
gradle -v
```

Copy

您应该会看到类似以下的内容：

```bash
Welcome to Gradle 6.5.1!

Here are the highlights of this release:
 - Experimental file-system watching
 - Improved version ordering
 - New samples

For more details see https://docs.gradle.org/6.5.1/release-notes.html


------------------------------------------------------------
Gradle 6.5.1
------------------------------------------------------------

Build time:   2020-06-30 06:32:47 UTC
Revision:     66bc713f7169626a7f0134bf452abde51550ea0a

Kotlin:       1.3.72
Groovy:       2.5.11
Ant:          Apache Ant(TM) version 1.10.7 compiled on September 1 2019
JVM:          11.0.7 (Ubuntu 11.0.7+10-post-Ubuntu-3ubuntu1)
OS:           Linux 5.4.0-26-generic amd64
```

Copy

您已经在Ubuntu系统上安装了最新版本的Gradle，然后就可以开始使用它了。

## 结论

我们已向您展示了如何在Ubuntu 20.04上安装Gradle。 现在，您可以访问[ Gradle文档](https://docs.gradle.org/current/userguide/userguide.html)的官方页面，并了解如何开始使用Gradle。