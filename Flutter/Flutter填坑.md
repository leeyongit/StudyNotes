# Flutter填坑

1. 执行flutter doctor 提示 `Android toolchain - develop for Android devices (Android SDK version 30.0.2)`

```sh
flutter doctor --android-licenses
```

2. Waiting for another flutter command to release the startup lock

```sh
rm -rf /Users/liyong/development/flutter/bin/cache/lockfile
```

3. **This is taking an unexpectedly long time.**

打开Flutter SDK：/Users/liyong/development/flutter/packages/flutter_tools/gradle/flutter.gradle
改为以下即可解决！

```ini
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

4. ### **[!] Your app isn't using AndroidX.**

打开android/gradle.properties

```ini
android.enableJetifier=true
android.useAndroidX=true
```

5. 运行flutter应用时，长时间卡在Running Gradle task 'assembleDebug'...

   解决方案https://segmentfault.com/a/1190000021451897

   /Users/liyong/.gradle/wrapper/dists

   https://services.gradle.org/distributions/

6. 执行 flutter packages get 命令报错
  
   > Your Flutter application is created using an older version of the Android
> embedding. It’s being deprecated in favor of Android embedding v2.

   解决办法：
   在android manifest.xml中 activity节点下增加配置

```xml
	<meta-data android:name="flutterEmbedding"  android:value="2" />
```

7. Setting ANDROID_HOME enviromental variable on Mac OS X

   A problem occurred configuring root project 'audioplayer'.

   > SDK location not found. Define location with sdk.dir in the local.properties file or with an ANDROID_HOME environment variable.

   `vim ~/.zshrc`

   ```
   export ANDROID_HOME=/Users/liyong/Library/Android/sdk
   export PATH=$ANDROID_HOME/platform-tools:$PATH
   export PATH=$ANDROID_HOME/tools:$PATH
   ```

   `source ~/.zshrc`