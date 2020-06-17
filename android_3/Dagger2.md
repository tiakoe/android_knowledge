## 介绍

Dagger2是一款基于Java注解来实现的完全在编译阶段完成依赖注入的开源库，主要用于模块间解耦、提高代码的健壮性和可维护性。Dagger2在编译阶段通过apt利用Java注解自动生成Java代码，然后结合手写的代码来自动帮我们完成依赖注入的工作。

**添加apt插件**

build.gradle(project)

```
 dependencies {
        classpath 'com.android.tools.build:gradle:3.3.2'
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
    }
```

**引入dagger2依赖**

bulid.gradle(app)

```
annotationProcessor 'com.google.auto.service:auto-service:1.0-rc4'
implementation 'com.google.dagger:dagger:2.6'
annotationProcessor 'com.google.dagger:dagger-compiler:2.6'
//    lifecycle
implementation 'androidx.lifecycle:lifecycle-extensions:2.2.0'
implementation 'androidx.lifecycle:lifecycle-runtime:2.2.0'
```

 注意：上面的两个版本尽量一致，要不然可能会抛异常 dagger.Provides missing element type



注解：

- @inject：声明依赖注入的对象
- @Moudle：依赖提供方，负责提供依赖中所需要的对象
- @Component：依赖注入组件，负责将依赖注入到依赖需求方。
- @Provides：会根据返回值类型在有此注解的方法中寻找应调用的方法



应用场景：Dagger2适用于构建复杂对象



Dagger2使用过程中我们通常接触到的注解主要包括：@Inject, @Module, @Provides, @Component, @Qulifier, @Scope, @Singleten。

- @Inject：@Inject有两个作用，一是用来标记需要依赖的变量，以此告诉Dagger2为它提供依赖；二是用来标记构造函数，Dagger2通过@Inject注解可以在需要这个类实例的时候来找到这个构造函数并把相关实例构造出来，以此来为被@Inject标记了的变量提供依赖；
- @Module：@Module用于标注提供依赖的类。你可能会有点困惑，上面不是提到用@Inject标记构造函数就可以提供依赖了么，为什么还需要@Module？很多时候我们需要提供依赖的构造函数是第三方库的，我们没法给它加上@Inject注解，又比如说提供以来的构造函数是带参数的，如果我们之所简单的使用@Inject标记它，那么他的参数又怎么来呢？@Module正是帮我们解决这些问题的。
- @Provides：@Provides用于标注Module所标注的类中的方法，该方法在需要提供依赖时被调用，从而把预先提供好的对象当做依赖给标注了@Inject的变量赋值；
- @Component：@Component用于标注接口，是依赖需求方和依赖提供方之间的桥梁。被Component标注的接口在编译时会生成该接口的实现类（如果@Component标注的接口为CarComponent，则编译期生成的实现类为DaggerCarComponent）,我们通过调用这个实现类的方法完成注入；
- @Qulifier：@Qulifier用于自定义注解，也就是说@Qulifier就如同Java提供的几种基本元注解一样用来标记注解类。我们在使用@Module来标注提供依赖的方法时，方法名我们是可以随便定义的（虽然我们定义方法名一般以provide开头，但这并不是强制的，只是为了增加可读性而已）。那么Dagger2怎么知道这个方法是为谁提供依赖呢？答案就是返回值的类型，Dagger2根据返回值的类型来决定为哪个被@Inject标记了的变量赋值。但是问题来了，一旦有多个一样的返回类型Dagger2就懵逼了。@Qulifier的存在正式为了解决这个问题，我们使用@Qulifier来定义自己的注解，然后通过自定义的注解去标注提供依赖的方法和依赖需求方（也就是被@Inject标注的变量），这样Dagger2就知道为谁提供依赖了。----一个更为精简的定义：当类型不足以鉴别一个依赖的时候，我们就可以使用这个注解标示；
- @Scope：@Scope同样用于自定义注解，我能可以通过@Scope自定义的注解来限定注解作用域，实现局部的单例；
- @Singleton：@Singleton其实就是一个通过@Scope定义的注解，我们一般通过它来实现全局单例。但实际上它并不能提前全局单例，是否能提供全局单例还要取决于对应的Component是否为一个全局对象。

我们提到@Inject和@Module都可以提供依赖，那如果我们即在构造函数上通过标记@Inject提供依赖，有通过@Module提供依赖Dagger2会如何选择呢？具体规则如下：

- 步骤1：首先查找@Module标注的类中是否存在提供依赖的方法。

- 步骤2：若存在提供依赖的方法，查看该方法是否存在参数。

- - a：若存在参数，则按从步骤1开始依次初始化每个参数；
  - b：若不存在，则直接初始化该类实例，完成一次依赖注入。



- 步骤3：若不存在提供依赖的方法，则查找@Inject标注的构造函数，看构造函数是否存在参数。

- - a：若存在参数，则从步骤1开始依次初始化每一个参数
  - b：若不存在，则直接初始化该类实例，完成一次依赖注入。

https://zhuanlan.zhihu.com/p/24454466





























