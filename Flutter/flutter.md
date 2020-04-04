

<!-- TOC -->

- [Mixin](#mixin)
- [Stream](#stream)

<!-- /TOC -->


## Mixin

**可以理解为「组合」，形如Java接口但更强大；**

**使用方式是通过 class A with B, C, ...，使A得到B、C的方法、字段；**

**除了普通类，可使用关键字 mixin 定义一种特殊的类，专门表示用来mixin(with)的；**

 

**mixin类不能继承或被继承，但是可以（或被）implements；**

**mixin类可以后接 on，例如 mixin A on B ，用以限定 A 只能被 B 及其子类所with；**

**mixin on 所指定的限定类必须包含一个无参无名构造函数；**



## Stream

> **Stream.fromFuture**:从Future创建新的单订阅流,当future完成时将触发一个data或者error，然后使用Down事件关闭这个流。
>
> **Stream.fromFutures**:从一组Future创建一个单订阅流，每个future都有自己的data或者error事件，当整个Futures完成后，流将会关闭。如果Futures为空，流将会立刻关闭。
>
> **Stream.fromIterable**:创建从一个集合中获取其数据的单订阅流。



