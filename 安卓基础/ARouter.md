

[TOC]



## 配置ARouter

> project  如果获取不到，则在gradle.properties   中进行配置项目名称

```
android {
  defaultConfig {
    ...
    javaCompileOptions {
      annotationProcessorOptions {
        arguments = [AROUTER_MODULE_NAME: project.getName()]
      }
    }
  }
}

dependencies {
  //api与compiler匹配使用，使用最新版可以保证兼容
  compile 'com.alibaba:arouter-api:1.4.0'
  annotationProcessor 'com.alibaba:arouter-compiler:1.2.1'
  ...
}

```

 

```
// 路由表自动加载插件
apply plugin: 'com.alibaba.arouter'

buildscript {

  repositories {
    google()
    jcenter()
  }
  dependencies {
    classpath 'com.android.tools.build:gradle:3.0.1'
    classpath "com.alibaba:arouter-register:1.0.2"
  }
}
```

###  

## 初始化 ARouter

```
@Override
public void onCreate() {
  super.onCreate();
  // 必须在初始化ARouter之前配置
  if (BuildConfig.DEBUG){
    // 日志开启
    ARouter.openLog();
    // 调试模式开启，如果在install run模式下运行，则必须开启调试模式
    ARouter.openDebug();
  }
  ARouter.init(this);
}
```





## 应用内携带参数跳转

```
//发送
ARouter.getInstance()
    .build(SecondActivity.PATH)
    .withString(SecondActivity.PARAM, "这是跳转携带的参数")
    .navigation();

//接收
Intent intent = getIntent();
    if (intent!=null){
      String param = intent.getStringExtra(PARAM);
      Toast.makeText(this, param, Toast.LENGTH_SHORT).show();
    }
    
// Activity返回结果处理
ARouter.getInstance()
        .build(ThreeActivity.PATH)
        .navigation(this, 100);
        
//setResult
  intent.putExtra(PARAM_RESULT,"这是返回携带的参数");
  setResult(RESULT_OK,intent);
```



 

## 判断App是否是Debug版本

```
/**
 * 判断App是否是Debug版本
 * @return {@code true}: 是<br>{@code false}: 否
 */
public static boolean isAppDebug() {
        String packageName = context.getPackageName();
        if (packageName == null || packageName.trim().length() == 0)
            return false;
        try {
            PackageManager pm = context.getPackageManager();
            ApplicationInfo ai = pm.getApplicationInfo(context.getPackageName(), 0);
            return ai != null && (ai.flags & ApplicationInfo.FLAG_DEBUGGABLE) != 0;
        } catch (PackageManager.NameNotFoundException e) {
            e.printStackTrace();
            return false;
        }
}
```


 

































































