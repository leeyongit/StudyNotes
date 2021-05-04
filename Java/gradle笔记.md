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

```bash
thrift -r -gen java europa.thrift
```

