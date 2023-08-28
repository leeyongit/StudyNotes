# Flutter run 下载Gradle慢

AndroidStudio Gradle下载速度慢解决方法
 1 找到工程项目的build.gradle
 2 注释掉 jcenter() ，改用国内阿里云的maven库地址
 这里注意，只需要分别修改 buildscript 和 allprojects 的 repositories 即可

```yaml
// Top-level build file where you can add configuration options common to all sub-projects/modules.
 
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
 
allprojects {
    repositories {
        // google()
        // jcenter()
        maven { url 'https://maven.aliyun.com/repository/google' }
        maven { url 'https://maven.aliyun.com/repository/jcenter' }
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public' }
    }
}
 
task clean(type: Delete) {
    delete rootProject.buildDir
}
```

> 找到fluttersdk目录,进入如下目录Flutter⁩ ▸ ⁨packages⁩ ▸ ⁨flutter_tools⁩ ▸ ⁨gradle⁩找到下 flutter.gradle文件

```yaml
buildscript {
    repositories {
        //google()
        //jcenter()
        maven{ url 'https://maven.aliyun.com/repository/jcenter'}
        maven{url 'http://maven.aliyun.com/nexus/content/groups/public'}
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.1.2'
    }
}
```

