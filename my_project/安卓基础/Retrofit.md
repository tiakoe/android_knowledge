[TOC]

<!-- TOC -->

- [使用方法](#使用方法)
    - [先定义你的网络接口](#先定义你的网络接口)
    - [创建Retrofit对象](#创建retrofit对象)
    - [获得网络请求API的实例](#获得网络请求api的实例)
    - [调用API方法](#调用api方法)
    - [执行网络请求](#执行网络请求)
- [上传文件案例](#上传文件案例)
- [@SerializedName](#serializedname)
- [@Expose](#expose)
- [相关博客：](#相关博客)

<!-- /TOC -->

retrofit的精髓就在于内部的动态代理模式。

<br/>

## 使用方法

```shell
implementation 'com.squareup.retrofit2:retrofit:2.7.0'
implementation 'com.squareup.retrofit2:converter-gson:2.7.0'
manifest：   <uses-permission android:name="android.permission.INTERNET" /> 
```

<br/>

### 先定义你的网络接口

```java
public interface GitHubService {
    @GET("users/{user}/repos")
    Call<List<Repo>> listRepos(@Path("user") String user);
}
```

<br/>

### 创建Retrofit对象

```java
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("https://api.github.com/")
        .build();
```

<br/>

### 获得网络请求API的实例

```java
GitHubService service = retrofit.create(GitHubService.class);
```

<br/>

### 调用API方法

```java
Call<List<Repo>> call = service.listRepos("octocat");
```

<br/>

### 执行网络请求

```java
// 同步
try {
    List<Repo> repos = call.execute().body();
} catch (IOException e) {
    e.printStackTrace();
}

// 异步
call.enqueue(new Callback<List<Repo>>() {
    @Override
    public void onResponse(Call<List<Repo>> call, Response<List<Repo>> response) {
        // 数据返回成功
    }

    @Override
    public void onFailure(Call<List<Repo>> call, Throwable t) {
       // 数据返回失败
    }
});
```

<br/>

支持的协议:`GET / POST / PUT / DELETE / HEAD / PATCH`

```java
interface PrintlnServer{ 
@GET("/")
Call<String> cate(@Query("cate") String cate);
 } 
//参数 cate 就是 它的 key，传入的值 就是它的 value
```

```java
@FormUrlEncoded
@POST("/")
Call<ResponseBody> example(@Field("name") String name,
                           @Field("occupation") String occupation);
// 需要注意的是 使用Field 的时候，要加 @FormUrlEncoded 用来格式化。
```

<br/>

## 上传文件案例

```java
public interface FileUploadService {
@Multipart
@POST("upload")
Call<ResponseBody> upload(@Part("description") RequestBody description,
@Part MultipartBody.Part file);
}
// 注意使用 注解 @Multipart

************************ 使用案例 ***********************

//先创建 service
FileUploadService service = retrofit.create(FileUploadService.class);

//构建要上传的文件
File file = new File(filename);
RequestBody requestFile =
RequestBody.create(MediaType.parse("application/otcet-stream"), file);

MultipartBody.Part body =
MultipartBody.Part.createFormData("aFile", file.getName(), requestFile);

String descriptionString = "This is a description";
RequestBody description =
RequestBody.create(MediaType.parse("multipart/form-data"), descriptionString);

Call<ResponseBody> call = service.upload(description, body);
call.enqueue(new Callback<ResponseBody>() {
@Override
public void onResponse(Call<ResponseBody> call,
Response<ResponseBody> response) {
System.out.println("success");
}

@Override
public void onFailure(Call<ResponseBody> call, Throwable t) {
t.printStackTrace();
}
});
```

[参考]: https://juejin.im/post/5e44ea086fb9a07c9e1c19b6

<br/>

## @SerializedName

注解来将对象里的属性跟json里字段对应值匹配起来。

<br/>

## @Expose

Gson 中的@Expose注解 用于声明当前的 参数 需要暴露给 JSON 进行序列化或反序列化. * 该注解只有在 你使用GsonBuilder去构造Gson时，同时调用excludeFieldsWithoutExposeAnnotation()方法才起作用 

 如：

```java
public class User {
     @Expose private String firstName;
     @Expose(serialize = false) private String lastName;
     @Expose (serialize = false, deserialize = false) 
     private String emailAddress;
     private String password;
   }
```

```
使用new Gson()实例化一个对象的话，那么@Expose 的注解是无效的，User中的参数都是会参与反序列化或序列化。
```

使用的是

```java
Gson gson = new GsonBuilder().excludeFieldsWithoutExposeAnnotation().create()
```

对Gson进行实例化，
 1.那么 没有被@Expose注解的password将不会参与系列化及反系列化。
 2.lastName不会参与序列化
 3.emailAddress 不会参与系列化及反序列化

 <br/>

<br/>

## 相关博客： 

https://juejin.im/post/5d15d1a66fb9a07ece67e3af#heading-9

https://juejin.im/post/5d0c8907518825325d507a5e



























