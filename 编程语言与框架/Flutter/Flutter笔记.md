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
flutter --version # 查看版本信息
flutter doctor 		# 检查flutter环境是否配置好
flutter doctor -v # 查看系统中flutter相关环境的配置

flutter emulators # 模拟器列表
flutter emulators --launch Pixel_3a_API_34 # 启动安卓模拟器

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

**Mac电脑用命令行工具创建Flutter项目**

```sh
sudo flutter create flutterdemo
cd flutterdemo
flutter run
```

通过--org指定你的包名，你指定的org将取代com.example。

```sh
sudo flutter create --org com.leeyongit flutterdemo
```

