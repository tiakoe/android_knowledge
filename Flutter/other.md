



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

