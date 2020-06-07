

<!-- TOC -->

- [RxJava原理总结：](#rxjava%e5%8e%9f%e7%90%86%e6%80%bb%e7%bb%93)
- [使用例子](#%e4%bd%bf%e7%94%a8%e4%be%8b%e5%ad%90)

<!-- /TOC -->

引入：

```
implementation 'io.reactivex.rxjava2:rxjava:2.2.11'
implementation 'io.reactivex.rxjava2:rxandroid:2.1.1'
```

<br/>

RxJava是基于Java虚拟机上的响应式扩展库，它通过使用可观察的序列将异步和基于事件的程序组合起来。

扩展了**观察者模式来支持数据/事件序列**，并且添加了操作符，这些操作符允许你声明性地组合序列，同时抽象出要关注的问题：比如低级线程、同步、线程安全和并发数据结构等。

<br/>

分为两部分：

1、订阅流程。

2、线程切换。

<br/>

响应式代码的基本组成部分是Observables和Subscribers。**Observable发送消息，而Subscriber则用于消费消息。**Observable会调用它的每个Subscriber的Subscriber.onNext()函数，并最终以Subscriber.onComplete()或者Subscriber.onError()结束。

<br/>

关键点是：Observables一般只有等到有Subscriber订阅它，才会开始发送消息

**热启动Observable和冷启动Observable**。**热启动Observable任何时候都会发送消息，即使没有任何观察者监听它。冷启动Observable只有在至少有一个订阅者的时候才会发送消息**。

<br/>

线程调度器

| 名称                           | 含义                                                    |
| ------------------------------ | ------------------------------------------------------- |
| Schedulers.io()                | 代表io操作的线程, 通常用于网络,读写文件等io密集型的操作 |
| Schedulers.computation()       | 代表CPU计算密集型的操作, 例如需要大量计算的操作         |
| Schedulers.newThread()         | 代表一个常规的新线程                                    |
| AndroidSchedulers.mainThread() | 代表Android的主线程                                     |

<br/>

CompositeDisposable提供的方法中，都是对事件的管理

- dispose():释放所有事件
- clear():释放所有事件，实现同dispose()
- add():增加某个事件
- addAll():增加所有事件
- remove():移除某个事件并释放
- delete():移除某个事件

<br/>

## RxJava原理总结：

- 被观察者 `（Observable）`  通过 订阅`（Subscribe）` **按顺序发送事件** 给观察者 `（Observer）`
- 观察者`（Observer）` **按顺序接收事件** & 作出对应的响应动作。

<br/>

## 使用例子

<br/>

```java
<--方式1：采用Observer 接口 -->
        // 1. 创建观察者 （Observer ）对象
        Observer<Integer> observer = new Observer<Integer>() {
        // 2. 创建对象时通过对应复写对应事件方法 从而 响应对应事件

            // 观察者接收事件前，默认最先调用复写 onSubscribe（）
            @Override
            public void onSubscribe(Disposable d) {
                Log.d(TAG, "开始采用subscribe连接");
            }
            
            // 当被观察者生产Next事件 & 观察者接收到时，会调用该复写方法 进行响应
            @Override
            public void onNext(Integer value) {
                Log.d(TAG, "对Next事件作出响应" + value);
            }

            // 当被观察者生产Error事件& 观察者接收到时，会调用该复写方法 进行响应
            @Override
            public void onError(Throwable e) {
                Log.d(TAG, "对Error事件作出响应");
            }
          
            // 当被观察者生产Complete事件& 观察者接收到时，会调用该复写方法 进行响应
            @Override
            public void onComplete() {
                Log.d(TAG, "对Complete事件作出响应");
            }
        };

<--方式2：采用Subscriber 抽象类 -->
// 说明：Subscriber类 = RxJava 内置的一个实现了 Observer 的抽象类，对 Observer 接口进行了扩展

// 1. 创建观察者 （Observer ）对象
Subscriber<String> subscriber = new Subscriber<Integer>() {

// 2. 创建对象时通过对应复写对应事件方法 从而 响应对应事件
            // 观察者接收事件前，默认最先调用复写 onSubscribe（）
            @Override
            public void onSubscribe(Subscription s) {
                Log.d(TAG, "开始采用subscribe连接");
            }

            // 当被观察者生产Next事件 & 观察者接收到时，会调用该复写方法 进行响应
            @Override
            public void onNext(Integer value) {
                Log.d(TAG, "对Next事件作出响应" + value);
            }

            // 当被观察者生产Error事件& 观察者接收到时，会调用该复写方法 进行响应
            @Override
            public void onError(Throwable e) {
                Log.d(TAG, "对Error事件作出响应");
            }

            // 当被观察者生产Complete事件& 观察者接收到时，会调用该复写方法 进行响应
            @Override
            public void onComplete() {
                Log.d(TAG, "对Complete事件作出响应");
            }
        };


<--特别注意：2种方法的区别，即Subscriber 抽象类与Observer 接口的区别 -->
// 相同点：二者基本使用方式完全一致（实质上，在RxJava的 subscribe 过程中，Observer总是会先被转换成Subscriber再使用）
// 不同点：Subscriber抽象类对 Observer 接口进行了扩展，新增了两个方法：
    // 1. onStart()：在还未响应事件前调用，用于做一些初始化工作
    // 2. unsubscribe()：用于取消订阅。在该方法被调用后，观察者将不再接收 & 响应事件
    // 调用该方法前，先使用 isUnsubscribed() 判断状态，确定被观察者Observable是否还持有观察者Subscriber的引用，如果引用不能及时释放，就会出现内存泄露
    
    observable.subscribe(observer);
 // 或者 observable.subscribe(subscriber)；
```

<br/>

链式调用

```java
// RxJava的链式操作
        Observable.create(new ObservableOnSubscribe<Integer>() {
        // 1. 创建被观察者 & 生产事件
            @Override
            public void subscribe(ObservableEmitter<Integer> emitter) throws Exception {
                emitter.onNext(1);
                emitter.onNext(2);
                emitter.onNext(3);
                emitter.onComplete();
            }
        }).subscribe(new Observer<Integer>() {
            // 2. 通过通过订阅（subscribe）连接观察者和被观察者
            // 3. 创建观察者 & 定义响应事件的行为
            @Override
            public void onSubscribe(Disposable d) {
                Log.d(TAG, "开始采用subscribe连接");
            }
            // 默认最先调用复写的 onSubscribe（）

            @Override
            public void onNext(Integer value) {
                Log.d(TAG, "对Next事件"+ value +"作出响应"  );
            }

            @Override
            public void onError(Throwable e) {
                Log.d(TAG, "对Error事件作出响应");
            }

            @Override
            public void onComplete() {
                Log.d(TAG, "对Complete事件作出响应");
            }

        });
    }
}

注：整体方法调用顺序：观察者.onSubscribe（）> 被观察者.subscribe（）> 观察者.onNext（）>观察者.onComplete() 
```

<br/>

其他：

```java
Observable.just("hello").subscribe(new Consumer<String>() {
            // 每次接收到Observable的事件都会调用Consumer.accept（）
            @Override
            public void accept(String s) throws Exception {
                System.out.println(s);
            }
        });
```

<br/>

重载方法：

```java
    public final Disposable subscribe() {}
    // 表示观察者不对被观察者发送的事件作出任何响应（但被观察者还是可以继续发送事件）

    public final Disposable subscribe(Consumer<? super T> onNext) {}
    // 表示观察者只对被观察者发送的Next事件作出响应
    public final Disposable subscribe(Consumer<? super T> onNext, Consumer<? super Throwable> onError) {} 
    // 表示观察者只对被观察者发送的Next事件 & Error事件作出响应

    public final Disposable subscribe(Consumer<? super T> onNext, Consumer<? super Throwable> onError, Action onComplete) {}
    // 表示观察者只对被观察者发送的Next事件、Error事件 & Complete事件作出响应

    public final Disposable subscribe(Consumer<? super T> onNext, Consumer<? super Throwable> onError, Action onComplete, Consumer<? super Disposable> onSubscribe) {}
    // 表示观察者只对被观察者发送的Next事件、Error事件 、Complete事件 & onSubscribe事件作出响应

    public final void subscribe(Observer<? super T> observer) {}
    // 表示观察者对被观察者发送的任何事件都作出响应
```

<br/>

<br/>

【参考文献】

https://blog.csdn.net/weixin_42046829/article/details/104833751

https://www.jianshu.com/p/6a89413f2ab1