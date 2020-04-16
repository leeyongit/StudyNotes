# Flutter 笔记


## flutter配置国内镜像

```sh
vim ~/.bash_profile
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
source ~/.bash_profile
```

## flutter常用命令

```sh
# 【flutter环境信息】
# 检查flutter环境是否配置好
flutter doctor
# 查看系统中flutter相关环境的配置
flutter doctor -v

# 【分支】
# 查看当前使用的分支：
flutter channel
# 切换分支：
# 切换beta分支
flutter channel beta
# 切换master分支
flutter channel master

# 【更新】
# 同时更新Flutter SDK和依赖包:
# 最好在项目的包含pubspec.yaml文件的目录执行：
flutter upgrade
# 获取pubspec.yaml文件中列出的所有依赖包：
flutter packages get
# 获取pubspec.yaml文件中列出的所有依赖包的最新版本
flutter packages upgrade
```

## Flutter填坑

**Waiting for another flutter command to release the startup lock**
```sh
rm -rf /Users/liyong/development/flutter/bin/cache/lockfile
```
**This is taking an unexpectedly long time.**

打开Flutter SDK：/Users/liyong/development/flutter/packages/flutter_tools/gradle/flutter.gradle
改为以下即可解决！

```sh
// Generated file. Do not edit.

buildscript {
    repositories {
        //google()
        //jcenter()
        maven { url 'https://maven.aliyun.com/repository/google' }
        maven { url 'https://maven.aliyun.com/repository/jcenter' }
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public' }

    }

    dependencies {
        classpath 'com.android.tools.build:gradle:3.1.2'
    }
}

```

**[!] Your app isn't using AndroidX.**

打开android/gradle.properties

```
android.enableJetifier=true
android.useAndroidX=true
```



