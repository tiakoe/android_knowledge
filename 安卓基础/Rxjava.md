

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







<br/>

【参考文献】

https://blog.csdn.net/weixin_42046829/article/details/104833751

