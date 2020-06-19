#springBoot部署



Spring Boot 还提供了另外两种启动项目的方式：

- 在项目路径下，使用命令行 `mvn spring-boot:run` 来启动，其效果和上面“启动主程序”的效果是一致的；
- 或者将项目打包，打包后以 Jar 包的形式来启动。

```sh
# 进行项目根目录
cd ../hello
# 执行打包命令
mvn clean package
# 以 Jar 包的形式启动
java -jar target/hello-0.0.1-SNAPSHOT.jar --server.port=8000
```

IDEA 快捷键：Alt+Enter 手动导包

[Mac安装、配置Maven](https://www.jianshu.com/p/abec70821e82)

java -jar -Xmx768m -Xms256m -Xlog:gc:gc.log /Volumes/work/europa-1.0/lib/europa-1.0.jar
