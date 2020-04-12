





androidx后内置LiveData/ViewModel/Lifecycle, 无需导入依赖

1. `LiveData`: 感知活跃(onStart)和非活跃状态(onPause)的数据观察者
2. `ViewModel`: 屏幕旋转自动保存数据, 或者意外销毁读写saveInstance数据. 保证数据安全
3. `Lifecyle`: 可以监听生命周期, 默认Activity和Fragment已经实现lifecycleOwner, 常用于框架封装中的生命周期处理.

