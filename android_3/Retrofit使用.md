

创建RetrofitManager；

```java
package com.users.xucanyou666.rxjava2_retrofit_mvp.util;


import java.util.concurrent.TimeUnit;

import okhttp3.OkHttpClient;
import retrofit2.Retrofit;
import retrofit2.adapter.rxjava2.RxJava2CallAdapterFactory;
import retrofit2.converter.gson.GsonConverterFactory;

import static com.users.xucanyou666.rxjava2_retrofit_mvp.contants.StaticQuality.BASE_URL;

/**
 * Retrofit单例工具类
 * 
 */
public class RetrofitManager {
    private Retrofit mRetrofit;


    //构造器私有，这个工具类只有一个实例
    private RetrofitManager() {
        OkHttpClient.Builder httpClientBuilder = new OkHttpClient.Builder();
        httpClientBuilder.connectTimeout(15, TimeUnit.SECONDS);
        mRetrofit = new Retrofit.Builder()
                .client(httpClientBuilder.build())
                .addConverterFactory(GsonConverterFactory.create())
                .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
                .baseUrl(BASE_URL)
                .build();
    }


    /**
     * 静态内部类单例模式
     *
     * @return
     */
    public static RetrofitManager getInstance() {
        return Inner.retrofitManager;
    }

    private static class Inner {
        private static final RetrofitManager retrofitManager = new RetrofitManager();
    }


    /**
     * 利用泛型传入接口class返回接口实例
     *
     * @param ser 类
     * @param <T> 类的类型
     * @return Observable
     */
    public <T> T createRs(Class<T> ser) {
        return mRetrofit.create(ser);
    }
}

```



定义方法

```java
public interface GetPoetryEntity {
    /**
     * 
     * @return 古诗词
     */
    @GET("all.json")
    Observable<PoetryEntity> getPoetry();
}

```





```java
public class PoetryModel implements IPoetryContract.IPoetryModel {
 
    /**
     *  
     * @return 古诗词
     */
    @Override
    public Observable<PoetryEntity> getPoetry() {
        return RetrofitManager.getInstance().createRs(GetPoetryEntity.class).getPoetry();
    }


}
```



```java
public interface IPoetryContract {
    interface IPoetryModel {
        /**
         *  
         * @return 诗歌
         */
        Observable<PoetryEntity> getPoetry();
    }
}
```



Retrofit使用OkHttpClient来实现网络请求



ConcurrentHashMap



可以扩展的部分有三个：

OkHttpClient：用于请求网络，其中可以添加拦截器、设置超时时间、log日志、

转化工厂（addConverterFactory）：常用于把Http访问得到的json字符串转换为Java数据对象

回调适配工厂（addCallAdapterFactory）：对网络工作对象callWorker的自动转换，把Retrofit中执行网络请求的Call对象，转换为接口中定义的Call对象。



## 动态代理

不需要一开始就创建代理对象，而是在运行时利用反射机制创建代理类，得到代理对象



```java
    public void main() {
        final IStarDao starDao = new SuperStarDao();

        IStarDao proxy = (IStarDao) Proxy.newProxyInstance(
                starDao.getClass().getClassLoader(),
                starDao.getClass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        /*--接活--*/
                        Object returnValue = method.invoke(starDao, args);//明星工作
                        /*--宣传工作--*/
                        return returnValue;
                    }
                });

        proxy.dowork();
    }

```



https://juejin.im/post/5e9fa2906fb9a03c5f7409e2











