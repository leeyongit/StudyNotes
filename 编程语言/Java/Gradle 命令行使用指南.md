# Gradle 命令行使用指南

## Gradle vs Gradlew 对比

### 核心差异
- **Gradlew (Gradle Wrapper)**
  - 自动下载预定义的 Gradle 版本
  - 确保编译环境统一
  - 存放路径: `~/.gradlew/wrapper/dists`
  - 版本由 `gradle/wrapper/gradle-wrapper.properties` 指定

- **Gradle**
  - 使用本地安装的 Gradle 版本
  - 存放路径: `~/.gradle`
  - 版本取决于本地安装

## 依赖管理命令

### 刷新依赖缓存
强制清除缓存并重新下载最新依赖：
```bash
./gradlew build --refresh-dependencies
```

## 调试命令选项

### 日志级别
```bash
# 显示详细错误栈信息
./gradlew compileDebug --stacktrace

# 显示详细信息日志
./gradlew compileDebug --info

# 显示调试级别日志
./gradlew compileDebug --debug
```

## 常用 Gradle 命令

### 基础命令
```bash
# 查看版本
gradle -v

# 执行指定任务
gradle [taskName]

# 静默模式执行任务
gradle -q [taskName]

# 执行指定项目的任务
gradle -b [projectName] [taskName]
```

### 信息查看
```bash
# 显示所有项目
gradle projects

# 显示所有任务
gradle tasks

# 启动图形界面
gradle --gui
gradle --gui&  # 后台运行

# 查看帮助
gradle --help
```