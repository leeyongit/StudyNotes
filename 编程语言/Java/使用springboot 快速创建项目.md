### 创建项目

要使用 Spring Boot 快速创建项目，可以使用 Spring Initializr，它是一个用于生成 Spring Boot 项目的 Web 界面。你可以访问 Spring Initializr 网站（https://start.spring.io/）来创建项目，也可以使用 curl 命令或浏览器来访问。

以下是使用 curl 命令在命令行中创建一个 Spring Boot 项目的示例：

```bash
curl https://start.spring.io/starter.zip -d dependencies=web -d language=java -d bootVersion=2.6.4 -d baseDir=my-spring-boot-project -o my-spring-boot-project.zip
```

这个命令会生成一个包含基本的 Spring Boot 项目结构的 zip 文件。你可以将其解压缩并导入到你喜欢的 IDE 中，比如 IntelliJ IDEA 或 Eclipse，然后开始编写代码。

如果你更喜欢使用浏览器，你可以访问 https://start.spring.io/，选择你需要的项目元数据，然后点击 "Generate" 按钮来下载生成的项目文件。

### 编译和打包

Spring Boot 还提供了另外两种启动项目的方式：

- 在项目路径下，使用命令行 `mvn spring-boot:run` 来启动，其效果和上面“启动主程序”的效果是一致的；
- 或者将项目打包，打包后以 Jar 包的形式来启动。

```sh
# 进行项目根目录
cd ../my-spring-boot-project
# 执行打包命令
mvn clean package
# 以 Jar 包的形式启动
java -jar target/my-spring-boot-project-0.0.1-SNAPSHOT.jar --server.port=8000
```

IDEA 快捷键：Alt+Enter 手动导包