

## 依赖注入开源库Dagger



```
apply plugin: 'dagger.hilt.android.plugin'
apply plugin: 'kotlin-kapt'

dependencies {
    implementation 'com.google.dagger:hilt-android:2.28-alpha'
    kapt 'com.google.dagger:hilt-android-compiler:2.28-alpha'
}
```



在项目的`build.gralde`中加入`classpath`

```
buildscript {
    dependencies {
       classpath 'com.google.dagger:hilt-android-gradle-plugin:2.28-alpha'
    }
}
```

依赖注入

```
@HiltAndroidApp
class App : Application() {
}
```



































