<!-- TOC -->

- [ListAdapter源码例子](#listadapter%e6%ba%90%e7%a0%81%e4%be%8b%e5%ad%90)
- [分割线](#%e5%88%86%e5%89%b2%e7%ba%bf)

<!-- /TOC -->

## ListAdapter源码例子

```java
 interface UserDao {
     {@literal @}Query("SELECT  FROM user ORDER BY lastName ASC")
     public abstract LiveData&lt;List&lt;User>> usersByLastName();
 }

 class MyViewModel extends ViewModel {
     public final LiveData&lt;List&lt;User>> usersList;
     public MyViewModel(UserDao userDao) {
         usersList = userDao.usersByLastName();
     }
 }

 class MyActivity extends AppCompatActivity {
     {@literal @}Override
     public void onCreate(Bundle savedState) {
         super.onCreate(savedState);
         //适配器和viewModel绑定，recycleview再来设置适配器；
         MyViewModel viewModel = ViewModelProviders.of(this).get(MyViewModel.class);
         RecyclerView recyclerView = findViewById(R.id.user_list);
         UserAdapter&lt;User> adapter = new UserAdapter();
         viewModel.usersList.observe(this, list -> adapter.submitList(list));
         recyclerView.setAdapter(adapter);
     }
 }

 class UserAdapter extends ListAdapter&lt;User, UserViewHolder> {
     public UserAdapter() {
         super(User.DIFF_CALLBACK);
     }
     {@literal @}Override
     public void onBindViewHolder(UserViewHolder holder, int position) {
         holder.bindTo(getItem(position));
     }
     public static final DiffUtil.ItemCallback&lt;User> DIFF_CALLBACK =
             new DiffUtil.ItemCallback&lt;User>() {
         {@literal @}Override
         public boolean areItemsTheSame(
                 {@literal @}NonNull User oldUser, {@literal @}NonNull User newUser) {
             // User properties may have changed if reloaded from the DB, but ID is fixed
             return oldUser.getId() == newUser.getId();
         }
         {@literal @}Override
         public boolean areContentsTheSame(
                 {@literal @}NonNull User oldUser, {@literal @}NonNull User newUser) {
             // NOTE: if you use equals, your object must properly override Object#equals()
             // Incorrectly returning false here will result in too many animations.
             return oldUser.equals(newUser);
         }
     }
 }
```







例子

```
  implementation 'com.android.support:recyclerview-v7:29.0.0'
  implementation 'androidx.swiperefreshlayout:swiperefreshlayout:1.0.0'  //下拉刷新
```

```xml
<androidx.recyclerview.widget.RecyclerView
    android:id="@+id/id_recycler_view"
    android:layout_gravity="center_horizontal"
    android:layout_width="match_parent"
    android:layout_height="200dp" />
```



```java
mRecyclerView.setLayoutManager(new LinearLayoutManager(this, LinearLayoutManager.VERTICAL
        , false));
mRecyclerView.setAdapter(new SubRvAdapter(this, CreateData.getStringList02()));


mRecyclerView.addOnScrollListener(new RecyclerView.OnScrollListener() {
    @Override
    public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
        super.onScrollStateChanged(recyclerView, newState);
        Log.d("aa", "正在滚动");
    }
});
```



```java
class SubRvAdapter extends RecyclerView.Adapter<SubRvAdapter.MyViewHolder> {

    private List<String> list;
    private final LayoutInflater inflater;

    public SubRvAdapter(Context context, List<String> list) {
        this.list = list;
        inflater = LayoutInflater.from(context);
    }

    @NonNull
    @Override
    public MyViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = inflater.inflate(R.layout.example_scroll_conflict_item, parent, false);
        return new MyViewHolder(view);
    }

    @Override
    public void onBindViewHolder(MyViewHolder holder, int position) {
        holder.tv.setText(list.get(position));
        holder.tv.setTextSize(20);
    }

    @Override
    public int getItemCount() {
        return list.size();
    }

    static class MyViewHolder extends RecyclerView.ViewHolder {

        private final TextView tv;

        private MyViewHolder(View itemView) {
            super(itemView);
            tv = itemView.findViewById(R.id.id_text_view_001);
        }
    }
}
```



## 分割线

```java
//添加Android自带的分割线
recyclerView.addItemDecoration(new DividerItemDecoration(this,DividerItemDecoration.VERTICAL));
```



方法二：

自定义形状：

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">

    <gradient
        android:centerColor="#ff00ff00" //绿色
        android:endColor="#ff0000ff"    //蓝色
        android:startColor="#ffff0000"  //红色
        android:type="linear" />
    <size android:height="3dp" />

</shape>
```

添加：

```kotlin
        //添加自定义分割线
        DividerItemDecoration divider = new DividerItemDecoration(this,DividerItemDecoration.VERTICAL);
        divider.setDrawable(ContextCompat.getDrawable(this,R.drawable.custom_divider));
        recyclerView.addItemDecoration(divider);
```













































































































