

[TOC]

<!-- TOC -->

- [Room介绍](#room%e4%bb%8b%e7%bb%8d)
- [添加依赖](#%e6%b7%bb%e5%8a%a0%e4%be%9d%e8%b5%96)
  - [其他依赖](#%e5%85%b6%e4%bb%96%e4%be%9d%e8%b5%96)
- [fallbackToDestructiveMigration()](#fallbacktodestructivemigration)

<!-- /TOC -->



<br/>

## Room介绍

Room是Google提供的一个ORM库。

<br/>

@Entity用来注解实体类，@Database通过`entities`属性引用被`@Entity`注解的类，并利用该类的所有字段作为表的列名来创建表。

@Dao用来注解一个接口或者抽象方法，该类的作用是提供访问数据库的方法。

@Database用来注解类，并且注解的类必须是继承自`RoomDatabase`的抽象类。该类主要作用是创建数据库和创建Daos（data access objects，数据访问对象）。`在使用@Database注解的类中必须定一个不带参数的方法，这个方法返回使用@Dao注解的类。`

<br/>

简言之：

- Entity：数据库实体，系统根据Entity类创建数据库，里面规定了`PrimaryKey`，列名、表名等数据库必备设定
- Dao：Database access object：定义了一些操作数据库的操作，比如说增删改查
- Database：可以认为是完整的数据库，完整的数据库包括数据库信息和数据库操作，也就是`Entity`和`Dao`

<br/>

## 添加依赖

```
dependencies {
    def lifecycle_version = "2.2.0"
    def room_version = "2.2.5"
    
    implementation "androidx.room:room-runtime:$room_version"
    annotationProcessor "androidx.room:room-compiler:$room_version"
    
}
```

<br/>

### 其他依赖

```groovy
// optional - Kotlin Extensions and Coroutines support for Room
implementation "androidx.room:room-ktx:$room_version"

// optional - RxJava support for Room
implementation "androidx.room:room-rxjava2:$room_version"

// optional - Guava support for Room, including Optional and ListenableFuture
implementation "androidx.room:room-guava:$room_version"

// Test helpers
testImplementation "androidx.room:room-testing:$room_version"
```

若不能成功添加，则配置

```
allprojects {
    repositories {
        maven { url "https://kotlin.bintray.com/kotlinx/" }
        google()
        jcenter()    
 	}
}
```

<br/>

<br/>

## fallbackToDestructiveMigration()

如果未找到将旧数据库架构迁移到最新架构版本的迁移，则允许Room破坏性地重新创建数据库表。
当设备上的数据库版本与最新架构版本不匹配时，Room将在数据库上运行必要的迁移。
如果找不到用于将数据库升级到当前版本的迁移集，它将抛出IllegalStateException。
您可以调用此方法来更改此行为以重新创建数据库，而不是崩溃。
如果数据库是通过资产或文件创建的，则Room将尝试使用相同的文件来重新创建数据库，否则，它将删除Room管理的数据库表中的所有数据。
要让Room仅在架构降级期间回退到破坏性迁移，请使用fallbackToDestructiveMigrationOnDowngrade（）。











































































