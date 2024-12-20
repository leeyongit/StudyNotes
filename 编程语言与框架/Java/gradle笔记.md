

## gradlew 和 gradle命令的区别

#### 定义区别：

##### Gradlew是包装器，自动下载包装器里定义好的gradle 版本，保证编译环境统一，gradle 是用本地的gradle版本。

#### 存放路径不同

对于系统gradle其存放在 ~/.gradle 下面。本系统可在该文件夹下面找到gradle2.12相关的内容。对于gradlew其存放在 ～/.gradlew/wrapper/dists 下面。这个下面有着许多版本的gradle。这些就是gradlew下载下来的gradle版本。

#### 使用gradle版本不同

若你在命令行使用gradlew 那么你使用的是存放在～/.gradlew/wrapper/dists路径下面的,通过在gradle/wrapper/gradle-wrapper.properties文件指定的gradle脚本。而你若使用gradle命令行，那么你使用的是~/.gradle下面的gradle脚本。




gradle 强制清除 缓存的依赖，再次主动下载，使用服务器上最新的依赖jar包

Mac:

```bash
./gradlew build --refresh-dependencies
```

# Gradle 命令之 --stacktrace ， --info ， --debug 用法

查看错误信息输出

```bash
 bash ./gradlew compileDebug --stacktrace  
```

> **命令列表：**
> gradlew compileDebug --stacktrace or
> gradle compileDebug --stacktrace 
> gradlew compileDebug --info
> gradlew compileDebug --debug

## 常用的Gradle命令

下面介绍一些Gradle中常用的命令操作

- 查看版本号: gradle -v
- 编译执行某个task: gradle Task名
- 静默编译执行某个task: gradle -q Task名（q表示quiet模式，表示编译执行Gradle脚本的过程中，只输出必要的信息. 除了quiet模式外，Gradle中还有其他三种模式）
- 编译执行某个Project中的某个task：gradle -b Project名 Task名（Gradle默认只执行build.gradle文件中，自定义其他文件xxx.gradle编译运行显式指定Project名称，这里的build.gradle其实表示的就是build Project）
- 显示所有的Project：gradle projects
- 显示所有的task：gradle tasks
- 显示gradle的gui：gradle --gui 或 gradle --gui&（后台运行）
- 查找所有的gradle命令: gradle --help