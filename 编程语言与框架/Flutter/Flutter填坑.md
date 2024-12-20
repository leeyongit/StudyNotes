# Flutter填坑

#### 1. Waiting for another flutter command to release the startup lock

```sh
rm -rf /Users/liyong/flutter/bin/cache/lockfile
```

#### **2. This is taking an unexpectedly long time.**

打开Flutter SDK：/Users/liyong/development/flutter/packages/flutter_tools/gradle/flutter.gradle
改为以下即可解决！

```groovy
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



#### 3. 提示 [!] Your app isn't using AndroidX.

打开android/gradle.properties

```ini
android.enableJetifier=true
android.useAndroidX=true
```



#### 4. 运行flutter应用时，长时间卡在Running Gradle task 'assembleDebug'...

运行时会卡在`Running 'gradle assembleDebug`, 因为Gradle的Maven仓库在国外, 可以使用阿里云的镜像地址

1. 打开项目 android — gradle — build.gradle

```yaml
buildscript {  
  ext.kotlin\_version = '1.3.50'  
  repositories {  
        // 这里做了修改，使用国内阿里的代理  
   	    allowInsecureProtocol = true
        maven { url 'https://maven.aliyun.com/repository/google' }
        maven { url 'https://maven.aliyun.com/repository/jcenter' }
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public' }
 }  
  dependencies {  
  classpath 'com.android.tools.build:gradle:3.5.0'  
  classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin\_version"  
  }  
}  
  
allprojects {  
  repositories {  
        //修改的地方  
        // google()
        // jcenter()
        allowInsecureProtocol = true
        maven { url 'https://maven.aliyun.com/repository/google' }
        maven { url 'https://maven.aliyun.com/repository/jcenter' }
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public' }
 }}
```

2.  打开flutter目录`flutter\packages\flutter_tools\gradle\flutter.gradle`

```yaml
buildscript {
    repositories {
        // 这里做了修改，使用国内阿里的代理
        // google()
        // jcenter()
        maven { url 'https://maven.aliyun.com/repository/google' }
        maven { url 'https://maven.aliyun.com/repository/jcenter' }
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public' }
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.5.0'
    }
}
```



#### 5. 执行 flutter packages get 命令报错

> Your Flutter application is created using an older version of the Android

> embedding. It’s being deprecated in favor of Android embedding v2.

   解决办法：
   在android manifest.xml中 activity节点下增加配置

```xml
	<meta-data android:name="flutterEmbedding"  android:value="2" />
```



#### 6. Setting ANDROID_HOME enviromental variable on Mac OS X

A problem occurred configuring root project 'audioplayer'.

> SDK location not found. Define location with sdk.dir in the local.properties file or with an ANDROID_HOME environment variable.

`vim ~/.zshrc`

```
export ANDROID_HOME=/Users/liyong/Library/Android/sdk
export PATH=$ANDROID_HOME/platform-tools:$PATH
export PATH=$ANDROID_HOME/tools:$PATH
```

`source ~/.zshrc`

