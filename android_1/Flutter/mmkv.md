



## mmkv_flutter 

> 测试版本号：^1.0.10

<br/>

<br/>

简介：可以实现键值对的存储，和SharedPreferences的功能相似。都可以将数据写入硬盘。原理是开辟一个共享内存区，通过mmap进行映射，不用担心是否延时，它会让系统调用该共享存储区的内容写入硬盘。**

 <br/>

> 与其他框架对比分析：
>
> 它的速度很快，在不使用await的情况下async执行，和sqflite: ^1.1.0的速度几乎持平；少量数据的情况下，SharedPreferences效率微高。
>
> 但是SharedPreferences存在数据量的限制，存储的数据量达到几百kb的时候就会报错，而mmkv和sqlite没有数据量的限制。
>
> mmkv的存储方式是以键值对的形式存储，`适用于数据量较多的零散数据`；而sqlite的数据存储是表的方式，适用于有结构性的批量数据存储；

 <br/>

> 缺点：会让内存增加，在读写数据的时候内存会明显上升；

 <br/>

【实验代码】

```dart
MmkvFlutter mmkv = await MmkvFlutter.getInstance();

int res1 = await mmkv.getInt('intfeesfdfdfKey');

bool res2 = await mmkv.getBool('inttfKey');

double res3 = await mmkv.getDouble('inttfdfKey');

int res4 = await mmkv.getLong('inttfdesfKey');

String res5 = await mmkv.getString('intthgffKey');

 

print([res1 == 0, res2 == false, res3 == 0.0, res4 == 0, res5 == ""]);

print([res1, res2, res3, res4, res5]);
```

 <br/>

【输出】

```dart
I/flutter ( 7773): [true, true, true, true, true]

I/flutter ( 7773): [0, false, 0.0, 0, ]
```

 <br/>

> 注意：

**在使用getXX(key)之前，如果key的value无值，或者key的value的类型不是XX，那么在执行一次getXX(key)后，会将该key的类型设置成XX；获取同一个key的不同类型值时，该插件会抛出异常，需要手动转化该值。**

**因此不要同时执行多个getXX（key），getYY（key）**

 <br/>

【实验代码】

```dart
  mmkv.setInt("key1", 2147483647);

  int temp = await mmkv.getInt("key1");

print(temp);

 

  mmkv.setInt("key1", -2147483648);

  temp = await mmkv.getInt("key1");

print(temp);

 

  mmkv.setLong("key2", 999999999999999);

  temp = await mmkv.getLong("key2");

print(temp);

 

  mmkv.setLong("key2", -999999999999999);

  temp = await mmkv.getLong("key2");

print(temp);

 

【输出】

I/flutter ( 7773): 2147483647

I/flutter ( 7773): -2147483648

I/flutter ( 7773): 999999999999999

I/flutter ( 7773): -999999999999999
```

 <br/>

【分析】

 **setInt设置的值的范围-2147483648 至 2147483647 （约21亿） 和java保持一致，在java中int和integer的范围都是该值；setLong设置的范围是15个9**

 <br/>

**值超出范围会报如下异常：**

```dart
// 出现异常。

// PlatformException (PlatformException(error, Unsupported value: java.lang.ClassCastException: java.lang.Long cannot be cast to java.lang.Integer, null))
```

 <br/>

**【实验代码】**

```dart
 mmkv.setInt("key3", null);  **//**会清除该key 值

 temp = await mmkv.getInt("key3");

 print(temp);

 

//可以为不同key设置不同的类型

mmkv.setInt("key", 99999);

print("setInt:${await mmkv.getInt("key")}");

mmkv.setBool("key", false);

print("setBool:${await mmkv.getBool("key")}");

mmkv.setString("key", "fffff");

print("setString:${await mmkv.getString("key")}");

 
```

<br/>

【输出】

```dart
I/flutter ( 7773): 0

I/flutter ( 7773): setInt:99999

I/flutter ( 7773): setBool:false

I/flutter ( 7773): setString:fffff
```

