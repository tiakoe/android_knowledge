[TOC]



当点击 Button 的时候，Presenter 调用 request 方法，在它的内部，通过 Model 调用 request 方法来请求数据，请求到数据之后，切换到主线程，调用 callback 的 onResult 方法来通知 Presenter，这时候 Presenter 就会调用 View 的 updateTv 方法来更新 TextView，完成了整个流程，可以发现，在整个过程从，View 和 Model 并没有直接交互，所有的交互都是在 Presenter 中进行的。

 

```java
interface MVPView {
    void updateTv(String text);
}


public class MVPActivity extends AppCompatActivity implements MVPView {
    private Button button;
    private TextView textView;
    private Presenter presenter;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_test);
        button = findViewById(R.id.button);
        textView = findViewById(R.id.text_view);
        presenter = new Presenter(this);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                presenter.request();
            }
        });
    }

    @Override
    public void updateTv(String text) {
        textView.setText(text);
    }
}


interface Callback {
    void onResult(String text);
}


public class HttpModel {
    private Callback callback;

    public HttpModel(Callback callback) {
        this.callback = callback;
    }

    private Handler handler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            callback.onResult((String) msg.obj);
        }
    };

    public void request() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Thread.sleep(2000);
                    Message msg = handler.obtainMessage();
                    msg.obj = "从网络获取到的数据";
                    handler.sendMessage(msg);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }
}


public class Presenter {
    private MVPView view;
    private HttpModel model;

    public Presenter(MVPView view) {
        this.view = view;
        model = new HttpModel(new Callback() {
            @Override
            public void onResult(String text) {
                Presenter.this.view.updateTv(text);
            }
        });
    }

    public void request() {
        model.request();
    }
}

```

**防止内存泄漏** 其实上面的代码存在内存泄漏的风险。试想一下，如果在点击 Button 之后，Model 获取到数据之前，退出了 Activity，此时由于 Activity 被 Presenter 引用，而 Presenter 正在进行耗时操作，会导致 Activity 的对象无法被回收，造成了内存泄漏，解决的方式很简单，在 Activity 退出的时候，把 Presenter 对中 View 的引用置为空即可。

 

```java
// Presenter.java
public void detachView() {
    view = null;
}

// MVPActivity.java
@Override
protected void onDestroy() {
    super.onDestroy();
    presenter.detachView();
}

```

另外还有一个问题，虽然这里 Activity 不会内存泄漏了，但是当 Activity 退出之后，Model 中请求数据就没有意义了，所以还应该在 detachView 方法中，把 Handler 的任务取消，避免造成资源浪费，这个比较简单，就不贴代码了。

 

### MVP 的封装

首先 Model、View 和 Presenter 都可能会有一些通用性的操作，所以可以分别定义三个对应的底层接口。

```java
interface BaseModel {
}

interface BaseView {
    void showError(String msg);
}

public abstract class BasePresenter<V extends BaseView, M extends BaseModel> {
    protected V view;
    protected M model;

    public BasePresenter() {
        model = createModel();
    }

    void attachView(V view) {
        this.view = view;
    }

    void detachView() {
        this.view = null;
    }

    abstract M createModel();
}

```

```java
interface TestContract {

    interface Model extends BaseModel {
        void getData1(Callback1 callback1);
        void getData2(Callback2 callback2);
        void getData3(Callback3 callback3);
    }

    interface View extends BaseView {
        void updateUI1();
        void updateUI2();
        void updateUI3();
    }

    abstract class Presenter extends BasePresenter<View, Model> {
        abstract void request1();
        abstract void request2();
        void request3() {
            model.getData3(new Callback3() {
                @Override
                public void onResult(String text) {
                    view.updateUI3();
                }
            });
        }
    }
}

```



### 使用Rxjava简化代码

```java
public class HttpModel {
    public Observable<String> request() {
        return Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> emitter) throws Exception {
                Thread.sleep(2000);
                emitter.onNext("从网络获取到的数据");
                emitter.onComplete();
            }
        }).subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread());
    }
}


public class Presenter {
    private MVPView view;
    private HttpModel model;

    public Presenter(MVPView view) {
        this.view = view;
        model = new HttpModel();
    }

    private Disposable disposable;

    public void request() {
        disposable = model.request()
                .subscribe(new Consumer<String>() {
                    @Override
                    public void accept(String s) throws Exception {
                        view.updateTv(s);
                    }
                }, new Consumer<Throwable>() {
                    @Override
                    public void accept(Throwable throwable) throws Exception {

                    }
                });
    }

    public void detachView() {
        view = null;
        if (disposable != null && !disposable.isDisposed()) {
            disposable.dispose();
        }
    }
}

```



MVP

Presenter类的构造函数中创建model对象；

初始化Presenter的时候，将view添加到Presenter中；（Presenter的基类中，添加view基类）



> BaseMvpActivity<V extends BaseView, P extends BasePresenter>》BaseActivity》AppCompatActivity
>
>
> BasePresenter<V extends BaseView>









































