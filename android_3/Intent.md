

## Intent使用

包含七种属性：Component、Action、Category、Data、Type、Extra、Flag

Component：指定要启动的组件；

<intent-filter>: 包含Action、Category、Data，对隐式的intent进行匹配；

设置Component属性的为显式intent；

隐式intent，安卓则根据其他属性进行查找要启动的activity；

### Data属性

data属性向Action属性提供操作的数据；Data属性由两部分构成，数据的URI和数据的MIME类型；

Action属性：  Intent.ACTION_VIEW    Data属性：  content://contacts/people/1   

显式intent：明确指定；

隐式intent：未明确指定； 设置action的；

```
//显式
Intent intent=new Intent();
intent.setClassName(“包名”，“全路径名”);
startActivity(intent);
```















