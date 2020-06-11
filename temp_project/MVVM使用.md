

在module级别的build.gradle上添加DataBinding的支持： 注意：如果要在library中使用，那么使用该library也要在build.gradle添加支持。

```
android{
   dataBinding{
          enabled = true
     }
}
```

 

## 官方demo

```java
    public class MyViewModel extends ViewModel {
        private MutableLiveData<List<User>> users;
        public LiveData<List<User>> getUsers() {
            if (users == null) {
                users = new MutableLiveData<List<User>>();
                loadUsers();
            }
            return users;
        }

        private void loadUsers() {
            // Do an asynchronous operation to fetch users.
        }
    }
    
```

```java
    public class MyActivity extends AppCompatActivity {
        public void onCreate(Bundle savedInstanceState) {
            // Create a ViewModel the first time the system calls an activity's onCreate() method.
            // Re-created activities receive the same MyViewModel instance created by the first activity.

            MyViewModel model = new ViewModelProvider(this).get(MyViewModel.class);
            model.getUsers().observe(this, users -> {
                // update UI
            });
        }
    }
    
```

**注意**：[`ViewModel`](https://developer.android.google.cn/reference/androidx/lifecycle/ViewModel?hl=zh-cn) 绝不能引用视图、[`Lifecycle`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle?hl=zh-cn) 或可能存储对 Activity 上下文的引用的任何类。



## 在 Fragment 之间共享数据

Activity 中的两个或更多 Fragment 需要相互通信是一种很常见的情况。想象一下主从 Fragment 的常见情况，假设您有一个 Fragment，在该 Fragment 中，用户从列表中选择一项，还有另一个 Fragment，用于显示选定项的内容。这种情况不太容易处理，因为这两个 Fragment 都需要定义某种接口描述，并且所有者 Activity 必须将两者绑定在一起。此外，这两个 Fragment 都必须处理另一个 Fragment 尚未创建或不可见的情况。

可以使用 [`ViewModel`](https://developer.android.google.cn/reference/androidx/lifecycle/ViewModel?hl=zh-cn) 对象解决这一常见的难点。这两个 Fragment 可以使用其 Activity 范围共享 [`ViewModel`](https://developer.android.google.cn/reference/androidx/lifecycle/ViewModel?hl=zh-cn) 来处理此类通信，如以所示：

```java
    public class SharedViewModel extends ViewModel {
        private final MutableLiveData<Item> selected = new MutableLiveData<Item>();

        public void select(Item item) {
            selected.setValue(item);
        }

        public LiveData<Item> getSelected() {
            return selected;
        }
    }

    public class MasterFragment extends Fragment {
        private SharedViewModel model;

        public void onViewCreated(@NonNull View view, Bundle savedInstanceState) {
            super.onViewCreated(view, savedInstanceState);
            model = new ViewModelProvider(requireActivity()).get(SharedViewModel.class);
            itemSelector.setOnClickListener(item -> {
                model.select(item);
            });
        }
    }

    public class DetailFragment extends Fragment {

        public void onViewCreated(@NonNull View view, Bundle savedInstanceState) {
            super.onViewCreated(view, savedInstanceState);
            SharedViewModel model = new ViewModelProvider(requireActivity()).get(SharedViewModel.class);
            model.getSelected().observe(getViewLifecycleOwner(), { item ->
               // Update the UI.
            });
        }
    }
    
```







































