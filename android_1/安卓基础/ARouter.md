

[TOC]

<br/>

<!-- TOC -->

- [配置ARouter](#配置arouter)
- [初始化 ARouter](#初始化-arouter)
- [应用内携带参数跳转](#应用内携带参数跳转)
- [判断App是否是Debug版本](#判断app是否是debug版本)
            - [路由中的分组概念](#路由中的分组概念)

<!-- /TOC -->

<br/>

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

 <br/>

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

<br/>

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

<br/>

<br/>

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

<br/>

 <br/>

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

<br/>

##### 路由中的分组概念

- SDK中针对所有的路径(/test/1 /test/2)进行分组，分组只有在分组中的某一个路径第一次被访问的时候，该分组才会被初始化
- 通过 @Route 注解主动指定分组，否则使用路径中第一段字符串(/*/)作为分组
- 注意：一旦主动指定分组之后，应用内路由需要使用 ARouter.getInstance().build(path, group) 进行跳转，手动指定分组，否则无法找到

<br/>

```
// 为每一个参数声明一个字段，并使用 @Autowired 标注
// URL中不能传递Parcelable类型数据，通过ARouter api可以传递Parcelable对象
```

<br/>


























































