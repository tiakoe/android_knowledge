
<!-- TOC -->


<!-- /TOC -->


**AsyncTask的本质是对Handler和线程池的封装**

```java
public abstract class AsyncTask<Params, Progress, Result> {
    private static final String LOG_TAG = "AsyncTask";
```



> Params：任务启动时的参数类型，例如接口的Url或文件路径。
>
> Progress：任务执行中的进度值类型。
>
> Result：任务执行完毕后的返回值，例如接口返回的JSON字符串或File对象。

 

运行流程：

1. 先实例化AsyncTask的子类，并调用execute方法启动任务。
2. 通过onPreExecute方法实现任务的前期工作，例如：弹个进度框。
3. 在doInBackground方法执行耗时/后台任务，此方法运行在子线程上。
4. 在doInBackground方法内可以通过publishProgress方法将处理的进度值发送到主线程。
5. onProgressUpdate方法在接到进度值后在主线程刷新界面。
6. 在doInBackground方法执行完毕或取消任务后onPostExecute方法会被响应，在此方法中可以将处理结果刷新到界面上。

 

默认多个AsyncTask实例是串行运行的；

若要并行执行，则：

executeOnExecutor(AsyncTask.THREAD_POOL_EXECUTOR)



注意事项：

AsyncTask的实例是一次性的，execute方法在多次调用时会抛异常。

在Activity或Fragment中使用AsyncTask时要记得在退出时取消正在执行的任务。

不要手动调用AsyncTask运行流程中的方法。

 



























































































































