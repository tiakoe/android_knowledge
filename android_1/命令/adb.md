


<!-- TOC -->

- [adb命令](#adb命令)
    - [优点](#优点)
    - [多设备](#多设备)
    - [单设备](#单设备)
    - [查看应用列表](#查看应用列表)
    - [安装应用](#安装应用)
    - [安装失败——提示代码含义](#安装失败提示代码含义)
    - [卸载应用](#卸载应用)
    - [清除数据](#清除数据)
    - [查看前台 Activity](#查看前台-activity)
    - [查看正在运行的 Services](#查看正在运行的-services)
    - [查看应用详细信息](#查看应用详细信息)
    - [查看应用安装路径](#查看应用安装路径)
    - [应用交互](#应用交互)
    - [启动应用/ 调起 Activity](#启动应用-调起-activity)
        - [options 参数及含义](#options-参数及含义)
    - [常见命令](#常见命令)

<!-- /TOC -->


<br/>

# adb命令（Android debug bridge）

<br/>

## 优点

> - 安装设备；
> - 管理设备预定端口；
> - 设备文件复制或粘贴。



<br/>

## 多设备

> adb [-d|-e|-s <serial-number>] <command>

<br/>

## 单设备

> adb <command>

<br/>

## 查看应用列表

> adb shell pm list packages [-f] [-d] [-e] [-s] [-3] [-i] [-u] [--user USER_ID] [FILTER]

| 参数 | 显示列表                |
| ---- | ----------------------- |
| 无   | 所有应用                |
| -f   | 显示应用关联的 apk 文件 |
| -d   | 只显示 disabled 的应用  |
| -e   | 只显示 enabled 的应用   |
| -s   | 只显示系统应用          |
| -3   | 只显示第三方应用        |
| -i   | 显示应用的 installer    |
| -u   | 包含已卸载应用          |
| ``   | 包名包含 `` 字符串      |

<br/>

```
查看所有应用         adb shell pm list packages

查看系统应用         adb shell pm list packages -s

查看第三方应用     adb shell pm list packages -3

包名包含某字符串    adb shell pm list packages huawei
```

<br/>

## 安装应用

> adb install [-l] [-r] [-t] [-s] [-d] [-g] <apk-file>

| 参数 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| -l   | 将应用安装到保护目录 /mnt/asec                               |
| -r   | 允许覆盖安装                                                 |
| -t   | 允许安装 AndroidManifest.xml 里 application 指定 `android:testOnly="true"` 的应用 |
| -s   | 将应用安装到 sdcard                                          |
| -d   | 允许降级覆盖安装                                             |
| -g   | 授予所有运行时权限                                           |

 <br/>

## 安装失败——提示代码含义

| 输出代码                                                     | 含义                                                         | 解决办法                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| INSTALL_FAILED_ALREADY_EXISTS                                | 应用已经存在，或卸载了但没卸载干净                           | `adb install` 时使用 `-r` 参数，或者先 `adb uninstall ` 再安装 |
| INSTALL_FAILED_INVALID_APK                                   | 无效的 APK 文件                                              |                                                              |
| INSTALL_FAILED_INVALID_URI                                   | 无效的 APK 文件名                                            | 确保 APK 文件名里无中文                                      |
| INSTALL_FAILED_INSUFFICIENT<br/>_STORAGE                     | 空间不足                                                     | 清理空间                                                     |
| INSTALL_FAILED_DUPLICATE<br/>_PACKAGE                        | 已经存在同名程序                                             |                                                              |
| INSTALL_FAILED_NO<br/>_SHARED_USER                           | 请求的共享用户不存在                                         |                                                              |
| INSTALL_FAILED_UPDATE<br/>_INCOMPATIBLE                      | 以前安装过同名应用，但卸载时数据没有移除；或者已安装该应用，但签名不一致 | 先 `adb uninstall ` 再安装                                   |
| INSTALL_FAILED_SHARED<br/>_USER_INCOMPATIBLE                 | 请求的共享用户存在但签名不一致                               |                                                              |
| INSTALL_FAILED_MISSING<br/>_SHARED_LIBRARY                   | 安装包使用了设备上不可用的共享库                             |                                                              |
| INSTALL_FAILED_REPLACE<br/>_COULDNT_DELETE                   | 替换时无法删除                                               |                                                              |
| INSTALL_FAILED_DEXOPT                                        | dex 优化验证失败或空间不足                                   |                                                              |
| INSTALL_FAILED_OLDER_SDK                                     | 设备系统版本低于应用要求                                     |                                                              |
| INSTALL_FAILED_CONFLICTING<br/>_PROVIDER                     | 设备里已经存在与应用里同名的 content provider                |                                                              |
| INSTALL_FAILED_NEWER_SDK                                     | 设备系统版本高于应用要求                                     |                                                              |
| INSTALL_FAILED_TEST_ONLY                                     | 应用是 test-only 的，但安装时没有指定 `-t` 参数              |                                                              |
| INSTALL_FAILED_CPU<br/>_ABI_INCOMPATIBLE                     | 包含不兼容设备 CPU 应用程序二进制接口的 native code          |                                                              |
| INSTALL_FAILED_MISSING<br/>_FEATURE                          | 应用使用了设备不可用的功能                                   |                                                              |
| INSTALL_FAILED_CONTAINER<br/>_ERROR                          | 1. sdcard 访问失败; 2. 应用签名与 ROM 签名一致，被当作内置应用。 | 1. 确认 sdcard 可用，或者安装到内置存储; 2. 打包时不与 ROM 使用相同签名。 |
| INSTALL_FAILED_INVALID<br/>_INSTALL_LOCATION                 | 1. 不能安装到指定位置; 2. 应用签名与 ROM 签名一致，被当作内置应用。 | 1. 切换安装位置，添加或删除 `-s` 参数; 2. 打包时不与 ROM 使用相同签名。 |
| INSTALL_FAILED_MEDIA<br/>_UNAVAILABLE                        | 安装位置不可用                                               | 一般为 sdcard，确认 sdcard 可用或安装到内置存储              |
| INSTALL_FAILED_VERIFICATION<br/>_TIMEOUT                     | 验证安装包超时                                               |                                                              |
| INSTALL_FAILED_VERIFICATION<br/>_FAILURE                     | 验证安装包失败                                               |                                                              |
| INSTALL_FAILED_PACKAGE<br/>_CHANGED                          | 应用与调用程序期望的不一致                                   |                                                              |
| INSTALL_FAILED_UID_CHANGED                                   | 以前安装过该应用，与本次分配的 UID 不一致                    | 清除以前安装过的残留文件                                     |
| INSTALL_FAILED_VERSION<br/>_DOWNGRADE                        | 已经安装了该应用更高版本                                     | 使用 `-d` 参数                                               |
| INSTALL_FAILED_PERMISSION<br/>_MODEL_DOWNGRADE               | 已安装 target SDK 支持运行时权限的同名应用，要安装的版本不支持运行时权限 |                                                              |
| INSTALL_PARSE_FAILED<br/>_NOT_APK                            | 指定路径不是文件，或不是以 `.apk` 结尾                       |                                                              |
| INSTALL_PARSE_FAILED<br/>_BAD_MANIFEST                       | 无法解析的 AndroidManifest.xml 文件                          |                                                              |
| INSTALL_PARSE_FAILED<br/>_UNEXPECTED_EXCEPTION               | 解析器遇到异常                                               |                                                              |
| INSTALL_PARSE_FAILED<br/>_NO_CERTIFICATES                    | 安装包没有签名                                               |                                                              |
| INSTALL_PARSE_FAILED<br/>_INCONSISTENT_CERTIFICATES          | 已安装该应用，且签名与 APK 文件不一致                        | 先卸载设备上的该应用，再安装                                 |
| INSTALL_PARSE_FAILED<br/>_CERTIFICATE_ENCODING               | 解析 APK 文件时遇到 `CertificateEncodingException`           |                                                              |
| INSTALL_PARSE_FAILED<br/>_BAD_PACKAGE_NAME                   | manifest 文件里没有或者使用了无效的包名                      |                                                              |
| INSTALL_PARSE_FAILED<br/>_BAD_SHARED_USER_ID                 | manifest 文件里指定了无效的共享用户 ID                       |                                                              |
| INSTALL_PARSE_FAILED<br/>_MANIFEST_MALFORMED                 | 解析 manifest 文件时遇到结构性错误                           |                                                              |
| INSTALL_PARSE_FAILED<br/>_MANIFEST_EMPTY                     | 在 manifest 文件里找不到找可操作标签（instrumentation 或 application） |                                                              |
| INSTALL_FAILED_INTERNAL<br/>_ERROR                           | 因系统问题安装失败                                           |                                                              |
| INSTALL_FAILED_USER<br/>_RESTRICTED                          | 用户被限制安装应用                                           |                                                              |
| INSTALL_FAILED_DUPLICATE<br/>_PERMISSION                     | 应用尝试定义一个已经存在的权限名称                           |                                                              |
| INSTALL_FAILED_NO<br/>_MATCHING_ABIS                         | 应用包含设备的应用程序二进制接口不支持的 native code         |                                                              |
| INSTALL_CANCELED<br/>_BY_USER                                | 应用安装需要在设备上确认，但未操作设备或点了取消             | 在设备上同意安装                                             |
| INSTALL_FAILED_ACWF<br/>_INCOMPATIBLE                        | 应用程序与设备不兼容                                         |                                                              |
| INSTALL_FAILED_TEST_ONLY                                     | APK 文件是使用 Android Studio 直接 RUN 编译出来的文件        | 通过 Gradle 的 assembleDebug 或 assembleRelease 重新编译，或者 Generate Signed APK |
| does not contain AndroidManifest.xml                         | 无效的 APK 文件                                              |                                                              |
| is not a valid zip file                                      | 无效的 APK 文件                                              |                                                              |
| Offline                                                      | 设备未连接成功                                               | 先将设备与 adb 连接成功                                      |
| unauthorized                                                 | 设备未授权允许调试                                           |                                                              |
| error: device not found                                      | 没有连接成功的设备                                           | 先将设备与 adb 连接成功                                      |
| protocol failure                                             | 设备已断开连接                                               | 先将设备与 adb 连接成功                                      |
| Unknown option: -s                                           | Android 2.2 以下不支持安装到 sdcard                          | 不使用 `-s` 参数                                             |
| No space left on device                                      | 空间不足                                                     | 清理空间                                                     |
| Permission denied ... sdcard ...                             | sdcard 不可用                                                |                                                              |
| signatures do not match the previously installed version; ignoring! | 已安装该应用且签名不一致                                     | 先卸载设备上的该应用，再安装                                 |

[参考]: https://juejin.im/post/5b5683bcf265da0f9b4dea96


 <br/>

`adb install` 实际是分三步完成：

1. push apk 文件到 /data/local/tmp。
2. 调用 pm install 安装。
3. 删除 /data/local/tmp 下的对应 apk 文件。

<br/>

## 卸载应用

> adb uninstall [-k] <package-name>
>
> -k 参数可选，表示卸载应用但保留数据和缓存目录。



<br/>

## 清除数据

> adb shell pm clear <package-name>
>
> 清除应用数据与缓存，效果相当于在设置里的应用信息界面点击了「清除缓存」和「清除数据」。

<br/>



## 查看前台 Activity

> adb shell dumpsys activity activities | grep mFocusedActivity



<br/>

## 查看正在运行的 Services

> adb shell dumpsys activity services [<package-name>]
>
> 指定 <package-name> 表示查看与某个包名相关的 Services，不指定表示查看所有 Services。<package-name> 不一定要给出完整的包名，可以只给一部分，那么所给包名相关的 Services 都会列出来。





<br/>

## 查看应用详细信息

> adb shell dumpsys package <package-name>
>
> 包括 Activity Resolver Table、Registered ContentProviders、包名、userId、安装后的文件资源代码等路径、版本信息、权限信息和授予状态、签名版本信息等。



<br/>

## 查看应用安装路径

> adb shell pm path <package-name>



<br/>

## 应用交互

与应用交互主要是使用 `am <intent>` 命令，常用的 `<command>` 如下：

| command                           | 用途                             |
| --------------------------------- | -------------------------------- |
| `start [options]<intent> `        | 启动 `<intent>` 指定的 Activity  |
| `startservice [options]<intent> ` | 启动 `<intent>` 指定的 Service   |
| `broadcast [options]<intent> `    | 发送 `<intent>` 指定的广播       |
| `force-stop <package-name>`       | 停止 `<package-name>` 相关的进程 |

 参数很灵活，和写 Android 程序时代码里的 Intent 相对应。

<br/>

##  启动应用/ 调起 Activity

> adb shell am start [options] <intent>

```
adb shell am start -a android.settings.SETTINGS                   # 打开系统设置页面 adb shell am start -a android.intent.action.DIAL -d tel:10086         # 打开拨号页面 adb shell am start -n com.android.mms/.ui.ConversationList                  # 打开短信会话列表
```

 <br/>

### options 参数及含义

| 选项                        | 含义                                                    |
| --------------------------- | ------------------------------------------------------- |
| -D                          | 启用调试                                                |
| -W                          | 等待启动完成                                            |
| --start-profiler `file`     | 启动分析器并将结果发送到 `file`                         |
| -P `file`                   | 类似于 --start-profiler，但当应用进入空闲状态时分析停止 |
| -R `count`                  | 重复 Activity 启动次数                                  |
| -S                          | 启动 Activity 前强行停止目标应用                        |
| --opengl-trace              | 启用 OpenGL 函数的跟踪                                  |
| --user `user_id` \| current | 指定要作为哪个用户运行；如果未指定，则作为当前用户运行  |

[参考]: https://juejin.im/post/5b5683bcf265da0f9b4dea96




<br/>

## 常见命令

> adb version
> adb start-server
> adb kill-server

> 以 root 权限运行
> adb root

> 指定 adb server 的网络端口，ADB的默认端口为 5037
> adb -P <port> start-server      

> 查询已连接的设备/模拟器列表
> adb devices



| adb pull <手机文件> <电脑文件> | 将手机内文件导入到电脑上(文件名均为全称) |
| ------------------------------ | ---------------------------------------- |
| adb push <电脑文件> <手机文件> | 将电脑中文件推送到手机上(文件名均为全称) |









































