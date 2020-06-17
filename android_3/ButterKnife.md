

在模块中使用时：

```
buildscript {
  repositories {
    mavenCentral()
    google()
   }
  dependencies {
    classpath 'com.jakewharton:butterknife-gradle-plugin:10.2.1'
  }
}
```

and then apply it in your module:

```
apply plugin: 'com.android.library'
apply plugin: 'com.jakewharton.butterknife'
```

```
class ExampleActivity extends Activity {
  @BindView(R2.id.user) EditText username;
  @BindView(R2.id.pass) EditText password;
...
}
```