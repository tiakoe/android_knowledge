

# LiveData 

如果观察者（由 [`Observer`](https://developer.android.google.cn/reference/androidx/lifecycle/Observer) 类表示）的生命周期处于 [`STARTED`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle.State#STARTED) 或 [`RESUMED`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle.State#RESUMED) 状态，则 LiveData 会认为该观察者处于活跃状态。LiveData 只会将更新通知给活跃的观察者。

- start和resume状态视为活跃状态；
- 注册但非活跃的不能收到消息；

您可以注册与实现 [`LifecycleOwner`](https://developer.android.google.cn/reference/androidx/lifecycle/LifecycleOwner) 接口的对象配对的观察者。有了这种关系，当相应的 [`Lifecycle`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle) 对象的状态变为 [`DESTROYED`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle.State#DESTROYED) 时，便可移除此观察者。 这对于 Activity 和 Fragment 特别有用，因为它们可以放心地观察 [`LiveData`](https://developer.android.google.cn/reference/androidx/lifecycle/LiveData) 对象而不必担心泄露（当 Activity 和 Fragment 的生命周期被销毁时，系统会立即退订它们）。

- 通过实现LifecycleOwner接口达到生命周期一致，自动退订

```java
    public class NameViewModel extends ViewModel {

    // Create a LiveData with a String
    private MutableLiveData<String> currentName;

        public MutableLiveData<String> getCurrentName() {
            if (currentName == null) {
                currentName = new MutableLiveData<String>();
            }
            return currentName;
        }

    // Rest of the ViewModel...
    }
    
```

```java
    public class NameActivity extends AppCompatActivity {

        private NameViewModel model;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);

            // Other code to setup the activity...

            // Get the ViewModel.
            model = ViewModelProviders.of(this).get(NameViewModel.class);

            // Create the observer which updates the UI.
            final Observer<String> nameObserver = new Observer<String>() {
                @Override
                public void onChanged(@Nullable final String newName) {
                    // Update the UI, in this case, a TextView.
                    nameTextView.setText(newName);
                }
            };

           //观察LiveData，并以LifecycleOwner和观察者的身份传入此活动。
            model.getCurrentName().observe(this, nameObserver);
        }
    }
    
```

```java
    button.setOnClickListener(new OnClickListener() {
        @Override
        public void onClick(View v) {
            String anotherName = "John Doe";
            model.getCurrentName().setValue(anotherName);
        }
    });
    
```



[`MediatorLiveData`](https://developer.android.google.cn/reference/androidx/lifecycle/MediatorLiveData) 是 [`LiveData`](https://developer.android.google.cn/reference/androidx/lifecycle/LiveData) 的子类，允许您合并多个 LiveData 源。只要任何原始的 LiveData 源对象发生更改，就会触发 `MediatorLiveData` 对象的观察者。



















