

# swiperefreshlayout

```
implementation "androidx.swiperefreshlayout:swiperefreshlayout:1.0.0"
```

如果侦听器确定不应该进行刷新，则必须调用**setRefreshing(false)来取消任何刷新的可视指示。**

要**禁用手势和进度动画，请在视图上调用setEnabled(false)**

**只能支持一个直接子视图**，也就是SwipeRefreshLayout只能包裹一个子视图即ListView或者RecycleView等

### 方法

isRefreshing() 返回当前状态是不是刷新状态

setColorSchemeResources(int... colorResIds)  设置下拉进度条的颜色，参数为可变参数`int... colorResIds`，参数是资源id也就是color里的资源，可以设置不同的颜色，每转一圈就显示一种颜色，按照给的参数顺序显示颜色。

setOnRefreshListener(SwipeRefreshLayout.OnRefreshListener listener)   设置刷新监听，需要重写onRefresh()方法，一**般从顶部下拉刷新时会调用此方法**，在此方法设置刷新数据具体逻辑以及设置下拉进度条消失等一些工作









 







































