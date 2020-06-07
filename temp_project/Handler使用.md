[TOC]



### handler用途：

- 发送message或Runnable对象给其他线程；
- 接收其他线程的消息



### Message属性

arg1、arg2、obj、what

少量整型变量arg1和arg2比setData更节约资源

obj存放任意数据，效率高于setData

what匹配Message的标识符，很重要

```
Message msg=Message.obtain();

msg.what=101;

Bundle bundle=new Bundle();

bundle.putInt();......

msg.setData(bundle);
```



### AsyncTask

```
private class task extends AsyncTask<String,Integer,Bitmap>
```

三个参数分别表示输入类型，执行中类型，输出类型；

对应着doInBackground接收参数、显示进度参数、doInBackground和onPostExecute传入的参数；

**注意：**

- task实例必须在ui线程中创建；execute必须在ui线程中调用
- task只能被执行一次，多次调用会出现异常；
- doInBackground和onPostExecute的参数必须对应；



onPreExecute():开始执行前的准备工作；

doInBackground（Params...):开始执行后台处理任务，处理耗时事务，并把结果返回。在后台处理中，调用publishProgress（Progress）方法来更新实时的任务进度。

onProgressUpdate（Progress...)：在publishProgress（Progress）方法被调用后，UI线程将调用这个方法从而在界面上展示任务的进展情况。

onPostExecute（Result）：后台处理执行完成后的操作，后台处理返回的值将在onPostExecute方法作为参数，传送结果给UI线程。



### http通信

安卓提供http通信接口有两个：

java接口：HttpURLConnection接口

Apache接口：HttpClient接口

```java
public class HttpImageActivity extends AppCompatActivity {

    private ImageView imageView;
    private Bitmap bitmap;
    private static final   String image_path = "http://image.biaobaiju.com/uploads/20190807/13/1565156399-SGrnbwhBjR.jpg";

    @SuppressLint("HandlerLeak")
    private Handler handler = new Handler() {
        @Override
        public void handleMessage(@NonNull Message msg) {
            super.handleMessage(msg);
            if (msg.what == 0x123) {
                imageView.setImageBitmap(bitmap);
                Log.d("ffff","fdf");
            }
        }
    };

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.http_image_activity);
        initView();
    }

    private void initView() {
        imageView = findViewById(R.id.image);
    }

    public void loadImage(View view) {
        new Thread(() -> {
            try {
                URL url=new URL(image_path);
                InputStream is=url.openStream();
                bitmap= BitmapFactory.decodeStream(is);
                handler.sendEmptyMessage(0x123);
                    is.close();
                    is=url.openStream();
                OutputStream os=openFileOutput("my_app_test_image.jpg",0);
                byte[]buff=new byte[1024];
                int hasRead=0;
                while ((hasRead=is.read(buff))>0){
                    os.write(buff,0,hasRead);
                }
                is.close();
                os.close();
            } catch (Exception e) {
                Log.d("ff","ffff");
                e.printStackTrace();
            }
        }).start();
    }
}

```



### 文件存储：

分为内部存储和外部存储：内部存储与APP私有，外部存储对于APP来说公有。

外部存储：

用户卸载应用的时候，系统仅仅会删除external根目录（getExternalFilesDir() ）下的相关文件。

外部存储是进行数据共享的方式之一；



**内部存储使用：**

保存文件到内部存储：

getFilesDir(): 返回File对象，代表当前应用下内部存储下的文件目录，地址为：data/data/包名/files/

getCacheDir():返回File对象，当前内部存储下的缓存目录，系统内部空间不够时，会自动选择删除缓存文件。地址为：/data/data/包名/cache/

构造新文件： File file=new File（context.getFilesDir(),filename);

对内部文件读写：openFileOutput(String name,int mode)和 openFileInput(String name);



**外部存储使用：**

常用操作--查询外部存储器的状态：getExternalStorageState（），若返回MEDIA_MOUNTED，表示处于挂载状态，可以读写。

为了在外部存储区存储文件，必须在manifest文件中添加相应的权限。

```java
String state Environment.getExternalStorageState();
if(Environment.MEDIA_MOUNTED.equals(state)||Environment.MEDIA_MOUNTED_READ_ONLY.equals(state)){
    File path=Environment.getExternalStorageDirectory();
    File file=new File(path,filename);
    FileInputStream fis;
    try{
        fis=new FileInputStream(file);
        BufferedReader bufferedReader=new BufferedReader(new InputStreamReader(fis));
        String data=bufferedReader.readLine();
        fis.close();
    }catch(Exception e){
        e.printStackTrace();
    }
}
```



获取下载缓存区的根路径：Environment.getDownloadCacheDirectory();

想要文件以私有的形式保存在外部存储区中，可以通过执行getExternalFilesDir（）来获取相应的目录，并且传递一个指示文件类型的参数。这样的文件保存在外部存储区的mnt/sdcard/包名/   下。

想要在应用删除时文件保留，可以用getExternalStoragePublicDirectory（）来存储可以被共享的文件。



## 权限

android系统中危险权限有9组，取得一组中某一个权限的授权后，则自动获取该组的所有权限。

权限检查：

调用checkSelfPermission（）》shouldShowRequestPermissionRationale() 》requestPermissions（）



## ContentProvider

其他应用程序通过ContentResolver来获取暴露的数据；

采用URI方式提供数据；`content://<authority>/<path>/id`

authority:是在manifest中指定的唯一值，表示当前应用的contentProvider

ContentProvider是一个抽象类，通过继承它实现其中的方法，来创建数据提供者。

getType（Uri uri）：返回uri路径上的数据类型，如果是集合型，返回字符串以“....cursor.dir/" 开头；

如果是单条数据，以...item/开头

### UriMatcher

用于匹配uri，添加uri，

安卓还提供一个操作uri字符串的工具类：ContentUris

withAppendedId（Uri contentUri，long id）：用于为路径上加id

parseId（Uri contentUri）：从路径中获取id

### ContentResolver 

ContentResolver resolver=getContentResolver（）；















































































