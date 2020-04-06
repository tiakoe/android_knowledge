





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



> 合并manifest生成日志位于build/outputs/logs的manifest-merger-<productFlavor>-report.txt



当library工程的minSdkVersion比主工程src/main中的minSdkVersion低时会产生冲突，此时需要添加overLibrary标记解决冲突

















































































