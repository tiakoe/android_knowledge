



## 底部导航栏BottomNavigationView 的bugly

<fragment标签替换成<androidx.fragment.app.FragmentContainerView  导致闪退，

```
<fragment
    android:id="@+id/nav_host_fragment"
    android:name="androidx.navigation.fragment.NavHostFragment"
```

替换后NavHostFragment访问失败



menu和fragment的name要一致；