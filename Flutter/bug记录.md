

<!-- TOC -->

- [安装闪退](#安装闪退)
- [model对象和json的Map不能相互转换，不能自动生成.g文件](#model对象和json的map不能相互转换不能自动生成g文件)
- [code runner插件运行失败](#code-runner插件运行失败)
- [vscode连接夜神模拟器](#vscode连接夜神模拟器)
- [使用模拟器启动报错](#使用模拟器启动报错)
- [vscode 运行单个文件](#vscode-运行单个文件)
- [vs连接不上手机](#vs连接不上手机)
- [升级导致的插件问题](#升级导致的插件问题)
- [TextFormField中自动获取焦点问题](#textformfield中自动获取焦点问题)
- [textspan 增加功能出现问题](#textspan-增加功能出现问题)

<!-- /TOC -->

<br/>

## 安装闪退

```
√ Built build\app\outputs\apk\debug\app-debug.apk.

Error connecting to the service protocol: failed to connect to http://127.0.0.1:50349/hMCNihoTAmo=/
```

> 解决方案
>
> 删除build文件夹

<br/>

## model对象和json的Map不能相互转换，不能自动生成.g文件

> 在vs的控制台执行命令无效
>
> cmd，win10系统自带的命令行才行，执行：
>
> flutter pub run build_runner build --delete-conflicting-outputs

<br/>

## code runner插件运行失败

```
/D:/software2/flutterSDK/flutter1.12/flutter/packages/flutter/lib/src/material/animated_icons.dart:9:8: Error: Not found: 'dart:ui'
import 'dart:ui' as ui show Paint, Path, Canvas;
```

> 在启动配置，ctrl+q，选择调试运行
>
> 在测试文件中main方法第一行添加： TestWidgetsFlutterBinding.ensureInitialized();

<br/>

## vscode连接夜神模拟器

> 问题：vscode始终连不上夜神模拟器，不管是否在安装目录执行命令，还是全部关闭adb服务，再执行命令重开，依然连接不上服务器

<br/>

> 解决方案：
>
> 主要原因： android 平台的 adb 和夜神模拟器的 adb 版本不一致导致 
>
> 将android studio平台的安装目录下的adb拷贝到夜神模拟器下，将adb（android studio的）替换adb、  nox-adb（夜神的）
>
> 进入CMD，转到夜神安装目录
>
> 1）D:\software2\nox_moniqi\Nox\bin
>
> 2）执行命令：nox_adb.exe connect 127.0.0.1:62001  ，连接到服务器。

<br/>

## 使用模拟器启动报错

```
E/flutter ( 1469): [ERROR:flutter/shell/gpu/gpu_surface_gl.cc(70)] Failed to setup Skia Gr context.
```

> 由于vscode的按钮启动方式需要硬件渲染，因此报错，所以采用软件启动方式
>
> 方法一：
>
> flutter run --enable-software-rendering
>
> 方法二：
>
> 在vscode的launch中添加如下参数：
>
> “ args”：[“ --enable-software-rendering”，“-d”，“ all”]

<br/>

## vscode 运行单个文件

>  在 vscode 中运行单个 dart 文件，运行正在编辑的 dart 文件： 
>
> 运行配置文件中添加：

```
{

   "name": "Dart",

   "program": "${file}",

   "request": "launch",

   "type": "dart"

  }

```



<br/>

## vs连接不上手机

> ctrl+alt+delete，找到服务详细信息栏，结束adb.exe

<br/>

## 升级导致的插件问题

```
出现异常。

MissingPluginException (MissingPluginException(No implementation found for method TextInput.setEditableSizeAndTransform on channel flutter/textinput))
```

```
解决方案：
try {
      _channel.invokeMethod<void>(
        'TextInput.setEditableSizeAndTransform',
        args,
      );
    } on MissingPluginException catch (e) {
      print(e);
    }

改源码结局
```

<br/>

## TextFormField中自动获取焦点问题

```
I/flutter (17536): ══╡ EXCEPTION CAUGHT BY FOUNDATION LIBRARY ╞════════════════════════════════════════════════════════
I/flutter (17536): The following assertion was thrown while dispatching notifications for FocusNode:
I/flutter (17536): RenderBox was not laid out: RenderEditable#d2d49 NEEDS-LAYOUT NEEDS-PAINT
I/flutter (17536): 'package:flutter/src/rendering/box.dart':
I/flutter (17536): Failed assertion: line 1687 pos 12: 'hasSize'
I/flutter (17536): Either the assertion indicates an error in the framework itself, or we should provide substantially
I/flutter (17536): more information in this error message to help you determine and fix the underlying cause.
I/flutter (17536): In either case, please report this assertion by filing a bug on GitHub:
I/flutter (17536):   https://github.com/flutter/flutter/issues/new?template=BUG.md
I/flutter (17536): When the exception was thrown, this was the stack:
I/flutter (17536): #2      RenderBox.size 
package:flutter/…/rendering/box.dart:1687
I/flutter (17536): #3      EditableTextState._updateSizeAndTransform 
package:flutter/…/widgets/editable_text.dart:1729
I/flutter (17536): #4      EditableTextState._openInputConnection 
package:flutter/…/widgets/editable_text.dart:1415
I/flutter (17536): #5      EditableTextState._openOrCloseInputConnectionIfNeeded 
package:flutter/…/widgets/editable_text.dart:1441
I/flutter (17536): #6      EditableTextState._handleFocusChanged 
package:flutter/…/widgets/editable_text.dart:1707
I/flutter (17536): #7      ChangeNotifier.notifyListeners 
package:flutter/…/foundation/change_notifier.dart:206
I/flutter (17536): #8      FocusNode._notify 
package:flutter/…/widgets/focus_manager.dart:808
I/flutter (17536): #9      FocusManager._applyFocusChange 
package:flutter/…/widgets/focus_manager.dart:1401
I/flutter (17536): (elided 12 frames from class _AssertionError and package dart:async)
I/flutter (17536): The FocusNode sending notification was:
I/flutter (17536):   FocusNode#811d5
```

<br/>

> 自动获取焦点存在问题，TextFormField中不能设置这个参数，或者设置为false （版本升级带来的问题）

<br/>

## textspan 增加功能出现问题

```
出现异常。
_AssertionError ('package:flutter/src/widgets/widget_span.dart': Failed assertion: line 103 pos 12: 'dimensions != null': is not true.)
```

> 替换成GestureDetector，更改触摸监听函数

<br/>



















































