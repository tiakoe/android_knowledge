

```
<service
    android:name=".aidl_test.TestService"
    android:enabled="true"
    android:exported="false">
    <intent-filter>
        <action android:name="com.a.http_module.TestService" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</service>
```

android:exported="false"

android:exported 是Android中的四大组件 Activity，Service，Provider，Receiver 四大组件中都会有的一个属性。总体来说它的主要作用是：是否支持其它应用调用当前组件。



exported 的默认值根据Activity中是否有intent filter 来定。没有任何的filter意味着这个Activity只有在详细的描述了他的class name后才能被唤醒 .这意味着这个Activity只能在应用内部使用，因为其它application并不知道这个class的存在。所以在这种情况下，它的默认值是false。从另一方面讲，如果Activity里面至少有一个filter的话，意味着这个Activity可以被其它应用从外部唤起，这个时候它的默认值是true。



- 不只有这个属性可以指定Activity是否暴露给其它应用，也可以使用permission来限制外部实体唤醒当前Activity































































