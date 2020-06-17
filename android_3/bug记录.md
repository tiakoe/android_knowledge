

## 65536问题

```
  - failed  
  - :app:mergeDexDebug  
  - Cannot fit requested classes in a single dex file (# fields: 74065 > 65536)  
  - com.android.tools.r8.CompilationFailedException: Compilation failed to complete  
  - com.android.tools.r8.utils.b: Error: null, Cannot fit requested classes in a single dex file (# fields: 74065 > 65536)  
  - Error: null, Cannot fit requested classes in a single dex file (# fields: 74065 > 65536)
```

## 解决  

```dart
android {
    compileSdkVersion 27
    defaultConfig {
        applicationId "com.fynnjason.app.androiddexdemo"
        minSdkVersion 19
        targetSdkVersion 27
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
        multiDexEnabled true //就是这句，添加进来
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}
```

```php
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:27.1.1'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
    implementation 'com.android.support:multidex:1.0.3' //就是这句，添加进来
}
```

```java
import android.support.multidex.MultiDexApplication;

public class MyApplication extends MultiDexApplication{
    
    @Override
    public void onCreate() {
        super.onCreate();
    }
    
}
```

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.fynnjason.app.androiddexdemo">

    <application
        android:name=".MyApplication" //就是这句，添加进来
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```







































































