---
typora-copy-images-to: java知识点总结images
---

1. Java异常都继承自类Throwable，Throwable子类有Error和Exception，其中Exception又分为运行时异常和编译时异常。编译时异常是未雨绸缪性质的异常，是防范，需要显示处理。运行时异常是程序员问题造成，并不强制进行显示处理。**运行异常和error一样不需要处理**，非运行异常需要捕获；

------

![计算机生成了可选文字: 虚拟机栈 VMStacks 栈帧 Frame 本地万法栈 NativeMethod Stack 方法区 MethodArea (java知识点总结images/clip_image001.png) 堆 Heap 阢寄存器 ProgramCounterRegister](java知识点总结images/clip_image001.png)



**final 变量：**

final 变量能被显式地初始化并且只能初始化一次。被声明为 final 的对象的引用不能指向不同的对象。但是 final 对象里的数据可以被改变。也就是说 **final 对象的引用不能改变，但是里面的值可以改变。**

final 修饰符通常和 static 修饰符一起使用来创建类常量。

**final 方法**

类中的 final 方法可以被子类继承，但是不能被子类修改。

声明 final 方法的主要目的是防止该方法的内容被修改。

如下所示，使用 final 修饰符声明方法。

**final 类**

final 类不能被继承，没有类能够继承 final 类的任何特性。

------



1. **内部类：**

1.内部类中不能定义静态成员，内部类可以直接访问外部类中的成员变量，

3.内部类可以定义在外部类的方法外面，也可以定义在外部类的方法体中

   3.1.在方法体外面定义的内部类的访问类型可以是public,protecte,默认的，private等4种类型，创建内部类的实例对象时，一定要先创建外部类的实例对象，然后用这个外部类的实例对象去创建内部类的实例对象

   3.2.方法内部定义的内部类前面不能有访问类型修饰符，就好像方法中定义的局部变量一样，但这种内部类的前面可以使用final或abstract修饰符。这种内部类对其他类是不可见的，其他类无法引用这种内部类，但是这种内部类创建的实例对象可以传递给其他类访问。

4.在方法外部定义的内部类前面可以加上static关键字，从而成为Static Nested Class，它不再具有内部类的特性，所有，从狭义上讲，它不是内部类



------



```
	public class Test {
	    private synchronized void a() {
	    }
	    private void b() {
	        synchronized (this) {
	        }
	    }
	    private synchronized static void c() {
	    }
	    private void d() {
	        synchronized (Test.class) {
	        }
	    }
	}
	同一个对象，分别调用方法a和b，锁住的是同一个对象
	同一个对象，分别调用方法b和c，锁住的不是同一个对象
	同一个对象，分别调用方法a、b、c，锁住的不是同一个对象

```

```
 public class Example{
	    String str=new String("good");
	    char[]ch={'a','b','c'};
	    public static void main(String args[]){
	        Example ex=new Example();
	        ex.change(ex.str,ex.ch);
	        System.out.print(ex.str+" and ");
	        System.out.print(ex.ch);
	    }
	    public void change(String str,char ch[]){
	   //引用类型变量，传递的是地址，属于引用传递。
	        str="test ok";
	        ch[0]='g';
	    }
	}
	输出：good and gbc

```

```
 public static void main(String args[]) {
	System.out.println(14^3);
	}
	^表示异或 就是相同是0 不同是1
	14是1110
	3是0011
	所以14^3=1101，即13

```

```
  package NowCoder;
	class Test {
	    public static void hello() {
	        System.out.println("hello");
	    }
	}
	public class MyApplication {
	    public static void main(String[] args) {
	        // TODO Auto-generated method stub
	        Test test=null;
	        test.hello();
	    }
	}
答案：能编译通过，并正确运行
```



**JAVA的初始化顺序：**

- 父类的静态成员初始化>父类的静态代码块>子类的静态成员初始化>子类的静态代码块>父类的代码块>父类的构造方法>子类的代码块>子类的构造方法
- 静态成员和静态代码块只有在类加载的时候执行一次，再次创建实例时，不再执行，因为只在方法区存在一份，属于一整个类。

```
	13. class Person {
	    String name = "No name";
	    public Person(String nm) {
	        name = nm;
	    }
	}
	class Employee extends Person {
	    String empID = "0000";
	    public Employee(String id) {
	        empID = id;
	    }
	}
	public class Test {
	    public static void main(String args[]) {
	        Employee e = new Employee("123");
	        System.out.println(e.empID);
	    }
	}
	结果：编译报错
	父类没有无参的构造函数，所以子类需要在自己的构造函数中显式调用父类的构造函数。
	纠正：
	 class Person {
	    String name = "No name";
	    public Person(String nm) {
	        name = nm;
	    }
	     
	}
	class Employee extends Person {
	    public Employee(String nm) {
	        super(nm);
	        // TODO Auto-generated constructor stub
	    }
	 
	    String empID = "0000";
	}
	public class Test {
	    public static void main(String args[]) {
	        Employee e = new Employee("123");
	        System.out.println(e.empID);
	    }
	}

```

------

**线程安全**

hashtable     concurrentHashMap synchronizedMap

**在接口中：abstract只能修饰类和方法，不能修饰字段**

一个Java源程序文件中定义几个类和接口，则编译该文件后生成几个以.class为后缀的字节码文件。

jre 判断程序是否执行结束的标准是（）  所有的前台线程执行完毕

------

Servlet的生命周期属于初始化阶段是（）。

1. 1. 加载Servlet类及.class对应的数据
   2. 创建ServletConfig对象
   3. 创建Servlet对象

```
class X{
	    Y y=new Y();
	    public X(){
	        System.out.print("X");
	    }
	}
	class Y{
	    public Y(){
	        System.out.print("Y");
	    }
	}
	public class Z extends X{
	    Y y=new Y();
	    public Z(){
	        System.out.print("Z");
	    }
	    public static void main(String[] args) {
	        new Z();
	    }
	}
	结果：YXYZ

```



下面选项中,哪些是interface中合法方法定义?()

public void main(String [] args);

boolean setFlag(Boolean [] test);

public float get(int x);



**super和this都只能位于构造器的第一行，而且不能同时使用，这是因为会造成初始化两次，this用于调用重载的构造器，super用于调用父类被子类重写的方法**



java语言的下面几种数组复制方法中，哪个效率最高？  **System.arraycopy**

 复制的效率：System.arraycopy>clone>Arrays.copyOf>for循环



截止JDK1.8版本,java并发框架支持锁包括?

读写锁、自旋锁、乐观锁

------

### 锁

读写锁实际是一种特殊的自旋锁，它把对共享资源的访问者划分成读者和写者，读者只对共享资源进行读访问，写者则需要对共享资源进行写操作。

**悲观锁**

总是假设最坏的情况，**每次去拿数据的时候都认为别人会修改**，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁（共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程）。

**乐观锁**

总是假设最好的情况，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号机制和CAS算法实现。**乐观锁适用于多读的应用类型，这样可以提高吞吐量，**像数据库提供的类似于write_condition机制，其实都是提供的乐观锁。

 **活锁**指的是任务或者执行者没有被阻塞，由于某些条件没有满足，**导致一直重复尝试—失败—尝试—失败的过程。处于活锁的实体是在不断的改变状态，活锁有可能自行解开。**

------



































