

<!-- TOC -->

- [ViewModel](#viewmodel)
- [ViewModelProvider](#viewmodelprovider)
  - [isAssignableFrom](#isassignablefrom)
- [ViewModel和onSaveIntance的区别](#viewmodel%e5%92%8consaveintance%e7%9a%84%e5%8c%ba%e5%88%ab)

<!-- /TOC -->

`ViewModelStore` 存储多个ViewModel的存储类, 开发者无需关心

`ViewModelStoreOwner` Fragment|Activity都属于其派生类. 在ViewModelProvider创建时使用.

ViewModel绑定生命周期的对象必须实现`ViewModelStoreOwner`. 该接口的职责是保存ViewModelStore对象.

<br/>

## ViewModel

ViewModel只能在屏幕旋转时保存数据, 而无法在内存回收Activity时保存数据(除非使用SavedState)

`ViewModel` 用于继承实现自定义的数据实体

<br/>

## ViewModelProvider

ViewModelProviders.Factory 接口是负责实例化 ViewModels 对象的启动装置。需要自己写实例化 ViewModel 对象的方法。

```
new ViewModelProvider.NewInstanceFactory();
new ViewModelProvider.AndroidViewModelFactory(getApplication());
```

AndroidViewModelFactory继承于NewInstanceFactory

<br/>

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

<br/>

### isAssignableFrom

确定此Class对象表示的类或接口是否与指定的Class参数表示的类或接口相同，或者是该类或接口的超类或超接口。如果是，则返回true；否则返回true。否则返回false。如果此Class对象表示原始类型，则如果指定的Class参数正是此Class对象，则此方法返回true；否则，此方法返回true。否则返回false。
具体地说，此方法测试是否可以通过标识转换或扩展引用转换将指定Class参数表示的类型转换为此Class对象表示的类型。





## ViewModel和onSaveIntance的区别

1. ViewModel可以保存复杂大量数据, onSaveIntance基于Bundle对象保存不能存储大量数据(抛出异常)
2. ViewModel无法在意外销毁时恢复数据, 只是配置变更(切换横竖屏)时数据依然存在

需要额外扩展依赖

```
implementation 'androidx.lifecycle:lifecycle-viewmodel-savedstate:1.0.0-alpha05'
```

绑定所有者的时候使用`SavedStateVMFactory`工厂实例

```
mSavedStateViewModel = ViewModelProviders.of(this, new SavedStateVMFactory(this))
                .get(SavedStateViewModel.class);
```

ViewModel需要创建一个构造函数

```
public class SavedStateViewModel extends ViewModel {
  
  // 自定义的构造函数
    public SavedStateViewModel(SavedStateHandle savedStateHandle) {
        mState = savedStateHandle; // mState即可以读写数据, 不需要你去处理生命周期
    }
}
```

`SavedStateHandle`能够保存和查询数据. 数据会和onSaveInstanse保存机制一样不会丢失.

```
public <T> MutableLiveData<T> getLiveData(@Nullable String key) 

public Set<String> keys()

public <T> T get(@NonNull String key) 

public <T> void set(@NonNull String key, @Nullable T value)

public boolean contains(@NonNull String key)

public <T> T remove(@NonNull String key)
```



