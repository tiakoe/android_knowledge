



retrofit的精髓就在于内部的动态代理模式。



```bash
依赖：implementation 'com.squareup.retrofit2:retrofit:2.5.0' 
添加网络权限：<uses-permission android:name="android.permission.INTERNET" /> 
```



```css
  1.定义接口类(封装URL地址和数据请求)
  2.实列化Retrofit
  3.通过Retrofit实列创建接口服务对象
  4.接口服务对象调用接口中的方法，获取Call对象
  5.Call对象执行请求(异步，同步请求)
```



```css
使用时需要在Gradlr添加依赖
Gsoncom.squareup.retrofit2:converter-gson:2.0.2

Jackson：com.squareup.retrofit2:converter-jackson:2.0.2 
```



Retrofit及OkHtto3区别

```undefined
Retrofit2使用注解设置请求内容
Retrofit2回调主线程，OkHttp3回调子线程
Retrofit2可以做数据解析转换
Retrofit2可以使用在REST ful网络请求.
```









































