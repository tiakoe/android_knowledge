



组件的gradle中配置：

```
//    dagger2
annotationProcessor 'com.google.auto.service:auto-service:1.0-rc4'
implementation 'com.google.dagger:dagger:2.17'
annotationProcessor 'com.google.dagger:dagger-compiler:2.17'
```

在APP的gradle中配置：` classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'`

```
buildscript {
    repositories {
        google()
        jcenter()
        
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:4.0.0'
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}
```



防止出现：`Cannot access androidx.lifecycle.HasDefaultViewModelProviderFactory`

添加如下：

```
//    lifecycle
    implementation 'androidx.lifecycle:lifecycle-extensions:2.2.0'
    implementation 'androidx.lifecycle:lifecycle-runtime:2.2.0'
```



参考： https://zhuanlan.zhihu.com/p/24454466



DataBinding是studio自带的。只需要在我们app build.gradle的android标签下加上：

```
dataBinding {
        enabled = true
    }
```





















