



# 元注解

@Retention：保留的范围，可选值有三种。

| RetentionPolicy | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| SOURCE          | 注解将被编译器丢弃（该类型的注解信息只会保留在源码里，源码经过编译后，注解信息会被丢弃，不会保留在编译好的class文件里），如 @Override |
| CLASS           | 注解在class文件中可用，但会被 VM 丢弃（该类型的注解信息会保留在源码里和 class 文件里，在执行的时候，不会加载到虚拟机中），请注意，当注解未定义 Retention 值时，默认值是 CLASS。 |
| RUNTIME         | 注解信息将在运行期 (JVM) 也保留，因此可以通过反射机制读取注解的信息（源码、class 文件和执行的时候都有注解的信息），如 @Deprecated |

@Target：可以用来修饰哪些程序元素，如 TYPE, METHOD, CONSTRUCTOR, FIELD, PARAMETER等，未标注则表示可修饰所有

@Inherited：是否可以被继承，默认为false

@Documented：是否会保存到 Javadoc 文档中



## SparseArray

在数据量少的时候可以替换hashmap，它比hashmap占用的内存更少，性能更好；

同样是线程不安全的，底层采用两个数组，一个存储key，一个存储value；



## 常用java数据结构

ArrayList 底层数组，速度快，增删慢，不安全，扩容当前1.5倍+1；

Vector 安全，底层数组，扩容当前1倍

LinkedList 不安全，底层双向链表；

 

HashMap key，value可为null；不安全，默认16，每次扩充原来2倍；

 

HashTable 键不可以重复，值可以重复，底层hash表，默认初始化大小11

，每次扩充为原来的2n+1

key，value都不允许为null；

 

 

TreeMap 键不可以重复，值可以重复；根据key升序排列，key实现Comparable接口；底层：二叉树，红黑树

 

ConcurrentHashMap 一旦初始化，不能进行扩容；默认16，理论支撑同时16个线程并发写

















































