



## JNI

1. Java的源文件非常容易被反编译，而通过Native语言生成的.so库文件则不容易被反编译。
2. 有时候会有一种情况就是当我们使用Java时需要使用到一些库来实现某些功能，但又由于这些库仅仅提供了一些Native语言的接口。
3. 使用Native语言编写的代码运行效率高，尤其体现在音频视频图片的处理等需要大量复杂运算的操作上。充分利用了硬件的性能。



JNI是`Java Native Interface`的简写，它可以使Java与其他语言(如C、C++)进行交互。

它是Java调用Native语言的一种**特性**，属于Java语言的范畴，与Android无关。



### 实现的步骤

1. 在Java中声明Native方法（需要调用的本地方法）
2. 通过 javac 编译 Java源文件（ 生成.class文件）
3. 通过 javah 命令生成JNI头文件（生成.h文件）
4. 通过Native语言实现在Java源码中声明的Native方法
5. 编译成.so库文件
6. 通过Java命令执行 Java程序，最终实现Java调用本地代码(借助so库文件)
   

简而言之：java中声明方法，c/c++中实现方法





















