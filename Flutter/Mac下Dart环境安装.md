# Mac下Dart环境安装

**安装dart**

只针对开发版进行安装

```sh
 brew tap dart-lang/dart
 brew install dart --devel
```

**环境变量的配置**

```sh
#Dart的配置
export DART_HOME=/usr/local/Homebrew/dart-sdk/bin
export PATH="${DART_HOME}:${PATH}"
```

**hello world**

运行代码：

```dart
void main(){
  print("Hello World!");
}
```

