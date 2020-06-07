



### 5.1 特点

1. 图形的形式展示其执行时间调用栈
2. 信息全面，包含所有进程

### 5.2 使用方式

```
Debug.startMethodTracing("文件名");

Debug.stopMethodTracing();
复制代码
```

在代码中相应位置的地方打入埋点即可， startMethodTracing 有3个构造参数分别是

1. tracePath：文件名/路径
2. bufferSize：文件的容量大小
3. flag：TRACE_COUNT_ALLOCS 只有默认的这一种

代码运行完成后，会在

> mnt/sdcard/Android/data/包名/files

生成一个.trace后缀的文件，可以用Profiler添加打开它。

> 当然也可以使用Profiler的录制功能，但是因为要测量启动时间，点击录制手速并不会那么的精准，所以采用埋点的方式获取trace文件进行分析。

### 5.3 性能分析

![img](https://user-gold-cdn.xitu.io/2020/5/8/171f05481791be88?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

打开文件后为上图所示

### 5.4 时间模式

上图标签 1 所示：wallclock time 和 cpu time

1. Wall Clock Time：从进程开始运行到结束，时钟走过的时间，这其中包含了进程在阻塞和等待状态的时间。
2. Thread Time ：就是CPU执行用户指令所用的时间。

> 注意：如果线程A执行函数b，但是因为函数b加了锁，线程A进入等待状态，那么wallclock time也是要计算时间的，而Thread time则只是计算CPU花费在它身上的时间。
>
> 一般根据经验来讲wall duration时间久说明耗时多，然后看Thread time 这个值说明cpu花费在其身上的时间多不多。不多的话基本可以直接异步（因为不抢占CPU）多的话需要合理调度好执行顺序。

### 5.5 Call Chart

上图标签 2 所示：Call Chart

![img](https://user-gold-cdn.xitu.io/2020/5/8/171f054818d8765d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

它的作用就是可以很直观的查看到底是哪里耗时比较久，x轴为调用的时间线，越宽的表示耗时越久，y轴为调用的深度，也就是调用的子方法。父类在最上面，很明显initBottomTab（）方法调用是最耗时的。

> 鼠标悬浮可以查看耗费时间，双击可以跳转到相应代码

- 橙色：系统方法
- 蓝色：第三方API（包括java语言的api）
- 绿色：App自身方法

简易图如下：

![img](https://user-gold-cdn.xitu.io/2020/5/8/171f054818c12d3f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### 5.6 Flame Chart

上图标签 3 所示：Flame Chart 又称之为火焰图

![img](https://user-gold-cdn.xitu.io/2020/5/8/171f05481c3b8a1b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

y 轴表示调用栈，每一层都是一个函数。调用栈越深，火焰就越高，顶部就是正在执行的函数，下方都是它的父函数。 x 轴表示抽样数，如果一个函数在 x 轴占据的宽度越宽，就表示它被抽到的次数多，即执行的时间长。注意，x 轴不代表时间，而是所有的调用栈合并后，按字母顺序排列的。

> 火焰图就是看顶层的哪个函数占据的宽度最大。只要有"平顶"（plateaus），就表示该函数可能存在性能问题。

#### 练习1：

![img](https://user-gold-cdn.xitu.io/2020/5/8/171f05484a465992?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

假如火焰图如上图所示，我们需要分析哪个函数吗？

> 答：最顶层的函数g()占用 CPU 时间最多。d()的宽度最大，但是它直接耗用 CPU 的部分很少。b()和c()没有直接消耗 CPU。因此，如果要调查性能问题，首先应该调查g()，其次是i()。 另外，从图中可知a()有两个分支b()和h()，这表明a()里面可能有一个条件语句，而b()分支消耗的 CPU 大大高于h()。

#### 与Call Chart区别

方法D对B(B1、B2和B3)进行多次调用，其中一些调用B对C(C1和C3)进行调用。如果用Call Chart表示，则为

![img](https://user-gold-cdn.xitu.io/2020/5/8/171f05484b37e7a0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

因为B1、B2和B3共享相同的序列调用者(A→D→B)聚合,如下所示。同样,C1和C3聚合,因为它们共享相同的序列调用者(A→D→B→C)注意不包括C2, 因为它有不同的调用者序列(A→D→C)。

![img](https://user-gold-cdn.xitu.io/2020/5/8/171f054872b8bdf2?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

那么如果使用火焰图，则表示：

![img](https://user-gold-cdn.xitu.io/2020/5/8/171f054867d1d2f0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

也就是说，收集相同的调用序列的相同方法被收集并表示为火焰图中的一个较长的栏(而不是将它们显示为多个更短的条）

### 5.7 Top Down

上图标签 4 所示： Top Down 显示一个函数调用列表，在该列表中展开函数节点会显示函数的被调用方

![img](https://user-gold-cdn.xitu.io/2020/5/8/171f05487a4ceef0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

看上图右边区域：

- Self:方法调用用于执行自己的代码而不是它的子类的时间量。
- Children：方法调用花费的时间用于执行其被调用者，而不是其自己的代码
- Total：方法的Self和Children的时间的总和。这表示应用程序执行方法调用的总时间量

### 5.8 Bottom Up

上图标签 5 所示： Bottom Up 显示一个函数调用列表，在该列表中展开函数节点将显示函数的调用方。

![img](https://user-gold-cdn.xitu.io/2020/5/8/171f0548856cfdc0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

Bottom Up选项卡对于那些消耗最多(或最少)CPU时间的方法的排序方法很有用。可以检查每个节点，以确定哪些调用者在调用这些方法上花费最多的CPU时间。

- Self:方法调用用于执行自己的代码而不是它的子类的时间量。
- Children：方法调用花费的时间用于执行其被调用者，而不是其自己的代码
- Total：方法的Self和Children的时间的总和。这表示应用程序执行方法调用的总和

简略图如下

![img](https://user-gold-cdn.xitu.io/2020/5/8/171f054888f2ec23?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

**缺点**： 在项目中用到的，最多的还是Call Chart 和 Top Down, 但是traceView的原理就是抓取所有线程的所有函数里的信息，所以会导致程序变慢， 所以常用的是SysTrace


