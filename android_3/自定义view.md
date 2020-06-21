



![image-20200615154520099](自定义view/image-20200615154520099.png)



View的高度：

- width = getRight() - getLeft();
- height = getBottom() - getTop();



## 源码解读：

MeasureSpec封装了从父级传递到子级的布局要求。

每个MeasureSpec代表对宽度或高度的要求。

MeasureSpec由大小和模式组成。

共有三种可能的模式：

UNSPECIFIED

父母没有对孩子施加任何约束。它可以是任何大小。

EXACTLY

父母已经确定了孩子的确切尺寸。不管孩子想要多大，都会给孩子以这些界限。

AT_MOST

子项可以根据需要的大小而定，最大可以达到指定的大小。

> 总结：没有约束、完全约束（设置具体尺寸）、半约束（设置最大尺寸）



| 公开方法                                                     |                                                              |
| :----------------------------------------------------------- | ------------------------------------------------------------ |
| 打开 静态 [整数](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-int/index.html) | `getMode(measureSpec: Int)`从提供的度量规范中提取模式。      |
| 打开 静态 [整数](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-int/index.html) | `getSize(measureSpec: Int)`从提供的度量规范中提取大小。      |
| 打开 静态 [整数](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-int/index.html) | `makeMeasureSpec(size: Int, mode: Int)`根据提供的大小和模式创建度量规范。 |
| 打开 静态 [字符串](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/index.html)！ | `toString(measureSpec: Int)`返回指定度量规范的String表示形式。 |











































































































































