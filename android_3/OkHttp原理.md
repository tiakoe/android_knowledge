



OkHttp内部实现通过一个责任链模式完成，将网络请求的各个阶段封装到各个链条中，实现各层的解耦。



```
//创建OkHttpClient
val client = OkHttpClient.Builder().build();

//创建请求
val request = Request.Builder()
           .url("https://wanandroid.com/wxarticle/list/408/1/json")
           .build()

//同步任务开启新线程执行
Thread {
    //发起网络请求
    val response = client.newCall(request).execute()
    if (!response.isSuccessful) throw IOException("Unexpected code $response")
    Log.d("okhttp_test", "response:  ${response.body?.string()}")
}.start()

```







## 责任链

为请求创建了一个接收者对象的链。

职责链上的处理者负责处理请求，客户只需要将请求发送到职责链上即可，无须关心请求的处理细节和请求的传递，所以职责链将请求的发送者和请求的处理者解耦了。





## addInterceptor与addNetworkInterceptor的区别

应用拦截器和网络拦截器

应用拦截器永远只会触发一次；而网络拦截器可能执行多次。























































































