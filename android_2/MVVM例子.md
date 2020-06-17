

在module级别的build.gradle上添加DataBinding的支持： 注意：如果要在library中使用，那么使用该library也要在build.gradle添加支持。

```
android{
   dataBinding{
          enabled = true
     }
}
```

创建对象

```java
 public class User {
    //名字
    private String name;
    //是否男的
    private boolean isMale;
    //年龄
    private int age;

    public String getName() {
        return name;
    }

    public User(String name, boolean isMale, int age) {
        this.name = name;
        this.isMale = isMale;
        this.age = age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public boolean isMale() {
        return isMale;
    }

    public void setMale(boolean male) {
        isMale = male;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}

```

布局文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>

        <variable
            name="user"
            type="com.android.databinding.User" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user.name}" />


        <!-- boolean 要转为String来显示 不然编译异常-->
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{String.valueOf(user.isMale)}" />
        <!-- age是int类型 必须转化为String 不然编译异常-->
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{String.valueOf(user.age)}" />

    </LinearLayout>
</layout>

```

activity

```java
public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //setContentView(R.layout.activity_main);

        ActivityMainBinding binding = DataBindingUtil.setContentView(this,R.layout.activity_main);
        User user = new User("狗狗",true,24);
        binding.setUser(user);
    }
}

```















































































