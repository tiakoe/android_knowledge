





back键会调用finsh(); 或者显示调用finsh();

onStart ,onResume , onPause , onStop , onDestroy ,均有可能在finsh()之后调用



5.0以上版本的时候 , startActivityForResult  为了获取到正常的回调,会导致singleTask 和singleinstance失效,android认为不同的task之间应该不能获取信息,为了不创建新的task,

如果重复的使用startActivityForResult启动自己, 无论什么启动模式,都会一直创建新的activity实例

```
adb shell dumpsys activity activities
```



























































