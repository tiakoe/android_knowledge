



```
final List<Widget> labels = <Widget>[
      ..._getDayHeaders(themeData.textTheme.caption, localizations),
    ];
```

```
GridView.custom(
              gridDelegate: _kDayPickerGridDelegate,
              childrenDelegate: SliverChildListDelegate(labels, addRepaintBoundaries: false),
              padding: EdgeInsets.zero,
            )
```





绘制原理：https://www.bookstack.cn/read/GSYFlutterBook/Flutter-9.md



