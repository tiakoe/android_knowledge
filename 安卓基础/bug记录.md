

[TOC]

<!-- TOC -->

- [Manifest merger failed](#manifest-merger-failed)
- [删除模块](#删除模块)
- [`.iml与.Idea`](#iml与idea)
- [gradle 构建failure](#gradle-构建failure)
- [ARouter No module name](#arouter-no-module-name)
- [组件跳转之applicationId](#组件跳转之applicationid)
- [64K问题](#64k问题)
- [android studio 出现问题，ARouter插件失效，导致跳转失败](#android-studio-出现问题arouter插件失效导致跳转失败)
- [组件之间的xml文件同名，默认跳转至起始组件](#组件之间的xml文件同名默认跳转至起始组件)

<!-- /TOC -->



<br/>

## Manifest merger failed

<br/>

```
Manifest merger failed with multiple errors, see logs
```

```sh
# 查看任务
./gradlew tasks
# 查看所有任务 包括缓存任务等
./gradlew tasks --all
# 对某个module [moduleName] 的某个任务[TaskName] 运行
./gradlew :moduleName:taskName
```

<br/>

> 合并manifest生成日志位于build/outputs/logs的manifest-merger-<productFlavor>-report.txt

<br/>

当library工程的minSdkVersion比主工程src/main中的minSdkVersion低时会产生冲突，此时需要添加overLibrary标记解决冲突

<br/>

## 删除模块

```
Unsupported Modules Detected: Compilation is not supported for following modules: base_module. Unfortunately you can't have non-Gradle Java modules and Android-Gradle modules in one project.
```

<br/>

删除.IDEA目录中的modules.xml

File->失效缓存/重新启动->失效缓存&重新启动

<br/>

## `.iml与.Idea`

```
.iml是 intellij idea的工程配置文件，里面是当前project的一些配置信息。
.Idea存放项目的配置信息，包括历史记录，版本控制信息等。

如果觉得看着麻烦的话，我们可以配置隐藏。点击file->Settings->File types,在右下角的Ignore files and folders中可以将.idea隐藏，但是似乎不可以隐藏.iml文件。
```

<br/>

## gradle 构建failure

```java
Error:Auto build failure: Internal error: (java.lang.ClassNotFoundException) com.google.wireless.android.sdk.stats.IntellijIndexingStats$Index
java.lang.ClassNotFoundException: com.google.wireless.android.sdk.stats.IntellijIndexingStats$Index
	at java.net.URLClassLoader.findClass(URLClassLoader.java:382)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	at com.intellij.util.indexing.counters.IndexCounters.<clinit>(IndexCounters.java:34)
	at com.intellij.util.indexing.impl.MapReduceIndex.<init>(MapReduceIndex.java:94)
	at com.intellij.util.indexing.impl.MapReduceIndex.<init>(MapReduceIndex.java:110)
	at org.jetbrains.jps.backwardRefs.index.CompilerReferenceIndex$CompilerMapReduceIndex.<init>(CompilerReferenceIndex.java:248)
	at org.jetbrains.jps.backwardRefs.index.CompilerReferenceIndex.<init>(CompilerReferenceIndex.java:84)
	at org.jetbrains.jps.backwardRefs.JavaCompilerBackwardReferenceIndex.<init>(JavaCompilerBackwardReferenceIndex.java:12)
	at org.jetbrains.jps.backwardRefs.JavaBackwardReferenceIndexWriter.initialize(JavaBackwardReferenceIndexWriter.java:80)
	at org.jetbrains.jps.incremental.java.JavaBuilder.buildStarted(JavaBuilder.java:149)
	at org.jetbrains.jps.incremental.IncProjectBuilder.runBuild(IncProjectBuilder.java:359)
	at org.jetbrains.jps.incremental.IncProjectBuilder.build(IncProjectBuilder.java:178)
	at org.jetbrains.jps.cmdline.BuildRunner.runBuild(BuildRunner.java:139)
	at org.jetbrains.jps.cmdline.BuildSession.runBuild(BuildSession.java:288)
	at org.jetbrains.jps.cmdline.BuildSession.run(BuildSession.java:121)
	at org.jetbrains.jps.cmdline.BuildMain$MyMessageHandler.lambda$channelRead0$0(BuildMain.java:228)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at java.lang.Thread.run(Thread.java:748)

```

```
implementation project(':base_module')
写成
implementation(':base_module')
```

<br/>

## ARouter No module name

<br/>

> ARouter::Compiler >>> No module name, for more information, look at gradle log.
>

`运行：gradlew build --info  发现base_module处未添加以下代码：`

```
javaCompileOptions {
    annotationProcessorOptions {
        arguments = [AROUTER_MODULE_NAME: project.getName(), AROUTER_GENERATE_DOC: "enable"]
    }
}
```

<br/>

<br/>

## 组件跳转之applicationId

报错：在login_module中重复定义

> 注意：不同模块之间用不同的分组

```
Type com.alibaba.android.arouter.routes.ARouter$$Group$$com is defined multiple times: F:\software2\android_project\android_project\app\build\intermediates\project_dex_archive\debug\out\com\alibaba\android\arouter\routes\ARouter$$Group$$com.dex, F:\software2\android_project\android_project\login_module\build\.transforms\8e37b0abe1e06c6db881f2e0b8bc9dae\classes\classes.dex
```

<br/>

* What went wrong:
Execution failed for task ':app:mergeDexDebug'.
> A failure occurred while executing com.android.build.gradle.internal.tasks.Workers$ActionFacade
> com.android.builder.dexing.DexArchiveMergerException: Error while merging dex archives:
> Learn how to resolve the issue at https://developer.android.com/studio/build/dependencies#duplicate_classes.
> Type com.alibaba.android.arouter.routes.ARouter$$Group$$com is defined multiple times: F:\software2\android_project\android_project\app\build\intermediates\project_dex_archive\debug\out\com\alibaba\android\arouter\routes\ARouter$$Group$$com.dex, F:\software2\android_project\android_project\login_module\build\.transforms\8e37b0abe1e06c6db881f2e0b8bc9dae\classes\classes.dex

<br/>

> 原因：在login_module中没有添加applicationId，而导致使用APP的applicationId，进而在login_module中添加`annotationProcessor 'com.alibaba:arouter-compiler:1.2.2'` 导致重复引用，不添加则又不能实现组件的跳转。
>

```
if (isDebugModule.toBoolean()) {
    applicationId "com.a.login_module"
}
```

<br/>

## 64K问题

```
解决：

1、在build.gradle文件的dependencies标签里添加com.android.support:multidex:$versions.multidex依赖

2、在build.gradle文件的defaultConfig标签里添加multiDexEnabled true配置

3、Application继承MultiDexApplication
```

<br/>

<br/>

```
 Caused by: com.alibaba.android.arouter.exception.InitException: ARouter::Init::Invoke init(context) first!
        at com.alibaba.android.arouter.launcher.ARouter.getInstance(ARouter.java:57)
        at com.a.login_module.LoginMainActivity.toLoginIn(LoginMainActivity.java:24)
        at java.lang.reflect.Method.invoke(Native Method) 
        at androidx.appcompat.app.AppCompatViewInflater$DeclaredOnClickListener.onClick(AppCompatViewInflater.java:397) 
        at android.view.View.performClick(View.java:5275) 
        at android.view.View$PerformClick.run(View.java:21559) 
        at android.os.Handler.handleCallback(Handler.java:815) 
        at android.os.Handler.dispatchMessage(Handler.java:104) 
        at android.os.Looper.loop(Looper.java:207) 
        at android.app.ActivityThread.main(ActivityThread.java:5845) 
        at java.lang.reflect.Method.invoke(Native Method) 
        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:907) 
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:768) 
```

<br/>

<br/>

## android studio 出现问题，ARouter插件失效，导致跳转失败

```
java.lang.AssertionError: Already disposed: Project (Disposed) ComponentDemo
	at com.intellij.openapi.components.impl.ComponentManagerImpl.lambda$throwAlreadyDisposed$1(ComponentManagerImpl.java:286)
	at com.intellij.openapi.application.ReadAction.lambda$run$1(ReadAction.java:53)
	at com.intellij.openapi.application.impl.ApplicationImpl.runReadAction(ApplicationImpl.java:932)
	at com.intellij.openapi.application.ReadAction.compute(ReadAction.java:57)
	at com.intellij.openapi.application.ReadAction.run(ReadAction.java:53)
	at com.intellij.openapi.components.impl.ComponentManagerImpl.throwAlreadyDisposed(ComponentManagerImpl.java:284)
	at com.intellij.openapi.components.impl.ComponentManagerImpl.getPicoContainer(ComponentManagerImpl.java:277)
	at com.intellij.openapi.components.impl.ComponentManagerImpl.getPicoContainer(ComponentManagerImpl.java:54)
	at com.intellij.openapi.components.ServiceManager.doGetService(ServiceManager.java:70)
	at com.intellij.openapi.components.ServiceManager.getService(ServiceManager.java:49)
	at com.intellij.util.indexing.FileBasedIndexScanRunnableCollector.getInstance(FileBasedIndexScanRunnableCollector.java:30)
	at com.intellij.util.indexing.FileBasedIndexImpl.collectScanRootRunnables(FileBasedIndexImpl.java:2342)
	at com.intellij.util.indexing.FileBasedIndexImpl.iterateIndexableFiles(FileBasedIndexImpl.java:2335)
	at com.intellij.util.indexing.FileBasedIndexImpl.projectIndexableFiles(FileBasedIndexImpl.java:1080)
	at com.intellij.psi.stubs.StubIndexImpl$StubIdListContainerAction.<init>(StubIndexImpl.java:586)
	at com.intellij.psi.stubs.StubIndexImpl$2.<init>(StubIndexImpl.java:282)
	at com.intellij.psi.stubs.StubIndexImpl.processElements(StubIndexImpl.java:282)
	at com.intellij.psi.stubs.StubIndex.getElements(StubIndex.java:121)
	at com.intellij.psi.stubs.StubIndex.getElements(StubIndex.java:109)
	at com.intellij.psi.impl.java.stubs.index.JavaAnnotationIndex.get(JavaAnnotationIndex.java:47)
	at com.intellij.psi.impl.search.AnnotatedElementsSearcher.lambda$getAnnotationCandidates$3(AnnotatedElementsSearcher.java:75)
	at com.intellij.openapi.application.impl.ApplicationImpl.runReadAction(ApplicationImpl.java:932)
	at com.intellij.openapi.application.ReadAction.compute(ReadAction.java:57)
	at com.intellij.psi.impl.search.AnnotatedElementsSearcher.getAnnotationCandidates(AnnotatedElementsSearcher.java:73)
	at com.intellij.psi.impl.search.AnnotatedElementsSearcher.execute(AnnotatedElementsSearcher.java:38)
	at com.intellij.psi.impl.search.AnnotatedElementsSearcher.execute(AnnotatedElementsSearcher.java:24)
	at com.intellij.util.ExecutorsQuery.processResults(ExecutorsQuery.java:31)
	at com.intellij.util.AbstractQuery.doProcessResults(AbstractQuery.java:91)
	at com.intellij.util.AbstractQuery.delegateProcessResults(AbstractQuery.java:114)
	at com.intellij.util.InstanceofQuery.processResults(InstanceofQuery.java:22)
	at com.intellij.util.AbstractQuery.doProcessResults(AbstractQuery.java:91)
	at com.intellij.util.AbstractQuery.forEach(AbstractQuery.java:83)
	at com.intellij.util.AbstractQuery.findAll(AbstractQuery.java:27)
	at com.alibaba.android.arouter.idea.extensions.NavigationLineMarker.navigate(NavigationLineMarker.kt:50)
	at com.intellij.codeInsight.daemon.NavigateAction.actionPerformed(NavigateAction.java:60)
	at com.intellij.openapi.actionSystem.ex.ActionUtil$1.run(ActionUtil.java:265)
	at com.intellij.openapi.actionSystem.ex.ActionUtil.performActionDumbAware(ActionUtil.java:282)
	at com.intellij.openapi.actionSystem.ex.ActionUtil.performActionDumbAwareWithCallbacks(ActionUtil.java:256)
	at com.intellij.openapi.editor.impl.EditorGutterComponentImpl.performAction(EditorGutterComponentImpl.java:1828)
	at com.intellij.openapi.editor.impl.EditorGutterComponentImpl.mouseReleased(EditorGutterComponentImpl.java:1788)
	at com.intellij.openapi.editor.impl.EditorImpl.processMouseReleased(EditorImpl.java:2297)
	at com.intellij.openapi.editor.impl.EditorImpl.access$9800(EditorImpl.java:122)
	at com.intellij.openapi.editor.impl.EditorImpl$MyMouseAdapter.lambda$runMouseReleasedCommand$1(EditorImpl.java:3812)
	at com.intellij.openapi.command.impl.CoreCommandProcessor.executeCommand(CoreCommandProcessor.java:220)
	at com.intellij.openapi.command.impl.CoreCommandProcessor.executeCommand(CoreCommandProcessor.java:178)
	at com.intellij.openapi.editor.impl.EditorImpl$MyMouseAdapter.runMouseReleasedCommand(EditorImpl.java:3814)
	at com.intellij.openapi.editor.impl.EditorImpl$MyMouseAdapter.mouseReleased(EditorImpl.java:3703)
	at java.awt.AWTEventMulticaster.mouseReleased(AWTEventMulticaster.java:290)
	at java.awt.Component.processMouseEvent(Component.java:6550)
	at javax.swing.JComponent.processMouseEvent(JComponent.java:3325)
	at java.awt.Component.processEvent(Component.java:6315)
	at java.awt.Container.processEvent(Container.java:2239)
	at java.awt.Component.dispatchEventImpl(Component.java:4899)
	at java.awt.Container.dispatchEventImpl(Container.java:2297)
	at java.awt.Component.dispatchEvent(Component.java:4721)
	at java.awt.LightweightDispatcher.retargetMouseEvent(Container.java:4904)
	at java.awt.LightweightDispatcher.processMouseEvent(Container.java:4535)
	at java.awt.LightweightDispatcher.dispatchEvent(Container.java:4476)
	at java.awt.Container.dispatchEventImpl(Container.java:2283)
	at java.awt.Window.dispatchEventImpl(Window.java:2746)
	at java.awt.Component.dispatchEvent(Component.java:4721)
	at java.awt.EventQueue.dispatchEventImpl(EventQueue.java:766)
	at java.awt.EventQueue.access$500(EventQueue.java:98)
	at java.awt.EventQueue$3.run(EventQueue.java:715)
	at java.awt.EventQueue$3.run(EventQueue.java:709)
	at java.security.AccessController.doPrivileged(Native Method)
	at java.security.ProtectionDomain$JavaSecurityAccessImpl.doIntersectionPrivilege(ProtectionDomain.java:74)
	at java.security.ProtectionDomain$JavaSecurityAccessImpl.doIntersectionPrivilege(ProtectionDomain.java:84)
	at java.awt.EventQueue$4.run(EventQueue.java:739)
	at java.awt.EventQueue$4.run(EventQueue.java:737)
	at java.security.AccessController.doPrivileged(Native Method)
	at java.security.ProtectionDomain$JavaSecurityAccessImpl.doIntersectionPrivilege(ProtectionDomain.java:74)
	at java.awt.EventQueue.dispatchEvent(EventQueue.java:736)
	at com.intellij.ide.IdeEventQueue.defaultDispatchEvent(IdeEventQueue.java:878)
	at com.intellij.ide.IdeEventQueue._dispatchEvent(IdeEventQueue.java:823)
	at com.intellij.ide.IdeEventQueue.lambda$dispatchEvent$8(IdeEventQueue.java:466)
	at com.intellij.openapi.progress.impl.CoreProgressManager.computePrioritized(CoreProgressManager.java:704)
	at com.intellij.ide.IdeEventQueue.dispatchEvent(IdeEventQueue.java:465)
	at java.awt.EventDispatchThread.pumpOneEventForFilters(EventDispatchThread.java:205)
	at java.awt.EventDispatchThread.pumpEventsForFilter(EventDispatchThread.java:116)
	at java.awt.EventDispatchThread.pumpEventsForHierarchy(EventDispatchThread.java:105)
	at java.awt.EventDispatchThread.pumpEvents(EventDispatchThread.java:101)
	at java.awt.EventDispatchThread.pumpEvents(EventDispatchThread.java:93)
	at java.awt.EventDispatchThread.run(EventDispatchThread.java:82)
```

<br/>

## 组件之间的xml文件同名，默认跳转至起始组件

xml命名：`组件名_文件名_类型`

实践证明以下代码并不起作用

```
android {
    resourcePrefix "moduleName_"
}
```





















