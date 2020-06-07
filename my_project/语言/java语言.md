



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