

<!-- TOC -->

- [ViewModel](#viewmodel)
- [ViewModelProvider](#viewmodelprovider)
  - [isAssignableFrom](#isassignablefrom)

<!-- /TOC -->

`ViewModelStore` 存储多个ViewModel的存储类, 开发者无需关心

`ViewModelStoreOwner` Fragment|Activity都属于其派生类. 在ViewModelProvider创建时使用.



## ViewModel

ViewModel只能在屏幕旋转时保存数据, 而无法在内存回收Activity时保存数据(除非使用SavedState)

`ViewModel` 用于继承实现自定义的数据实体



## ViewModelProvider

ViewModelProviders.Factory 接口是负责实例化 ViewModels 对象的启动装置。需要自己写实例化 ViewModel 对象的方法。

```
new ViewModelProvider.NewInstanceFactory();
new ViewModelProvider.AndroidViewModelFactory(getApplication());
```

AndroidViewModelFactory继承于NewInstanceFactory



AndroidViewModelFactory源码：

```
@NonNull
@Override
public <T extends ViewModel> T create(@NonNull Class<T> modelClass) {
    if (AndroidViewModel.class.isAssignableFrom(modelClass)) {
        //noinspection TryWithIdenticalCatches
        try {
            return modelClass.getConstructor(Application.class).newInstance(mApplication);
        } catch (NoSuchMethodException e) {
            throw new RuntimeException("Cannot create an instance of " + modelClass, e);
        } catch (IllegalAccessException e) {
            throw new RuntimeException("Cannot create an instance of " + modelClass, e);
        } catch (InstantiationException e) {
            throw new RuntimeException("Cannot create an instance of " + modelClass, e);
        } catch (InvocationTargetException e) {
            throw new RuntimeException("Cannot create an instance of " + modelClass, e);
        }
    }
    return super.create(modelClass);
}
```

### isAssignableFrom

确定此Class对象表示的类或接口是否与指定的Class参数表示的类或接口相同，或者是该类或接口的超类或超接口。如果是，则返回true；否则返回true。否则返回false。如果此Class对象表示原始类型，则如果指定的Class参数正是此Class对象，则此方法返回true；否则，此方法返回true。否则返回false。
具体地说，此方法测试是否可以通过标识转换或扩展引用转换将指定Class参数表示的类型转换为此Class对象表示的类型。