

## Rxjava使用

```java
/**
 * 得到诗歌
 * mPoetryModel.getPoetry()返回：Observable<PoetryEntity>
 */
@Override
public void getPoetry() {
    Observable observable = mPoetryModel.getPoetry().doOnSubscribe(new Consumer<Disposable>() {
        @Override
        public void accept(Disposable disposable) throws Exception {
            addDisposable(disposable);
        }
    });
    observable = RxJavaUtil.toSubscribe(observable);
    observable.subscribe(new Observer<PoetryEntity>() {
        @Override
        public void onSubscribe(Disposable d) {
        }

        @Override
        public void onNext(PoetryEntity poetryEntity) {
            mPoetryEntity = poetryEntity;
        }

        @Override
        public void onError(Throwable e) {
            getMvpView().onError(e.getMessage());
            Log.d(TAG, "onError: " + e.getMessage());
        }

        @Override
        public void onComplete() {
            if (mPoetryEntity != null) {
                getMvpView().searchSuccess(mPoetryEntity.getAuthor());
            }
        }
    });

}
```





#### 关于disposable

Disposable类

- dispose():主动解除订阅
- isDisposed():查询是否解除订阅 true 代表 已经解除订阅



#### CompositeDisposable类

> 1、可以快速解除所有添加的Disposable类.
>  2、每当我们得到一个Disposable时就调用CompositeDisposable.add()将它添加到容器中, 在退出的时候, 调用CompositeDisposable.clear() 即可快速解除.



```java
    /**
     * 解绑View,该方法在BaseMvpActivity类中被调用
     */
    public void detachView() {
        baseView = null;
        // 在界面退出等需要解绑观察者的情况下调用此方法统一解绑，防止Rx造成的内存泄漏
        if (mCompositeDisposable != null) {
            mCompositeDisposable.dispose();
        }
    }
```



```java
private final CompositeDisposable disposables = new CompositeDisposable();


// adding an Observable to the disposable
disposables.add(sampleObservable()
                   .subscribeOn(Schedulers.io())
                   .observeOn(AndroidSchedulers.mainThread())
                   .subscribeWith(new DisposableObserver<String>() {
                       @Override
                       public void onComplete() {
                       }

                       @Override
                       public void onError(Throwable e) {
                       }

                       @Override
                       public void onNext(String value) {
                       }
                   }));

       static Observable<String> sampleObservable() {
           return Observable.defer(new Callable<ObservableSource<? extends String>>() {
               @Override
               public ObservableSource<? extends String> call() throws Exception {
                   // Do some long running operation
                   SystemClock.sleep(2000);
                   return Observable.just("one", "two", "three", "four", "five");
               }
           });
       }                


// Using clear will clear all, but can accept new disposable
disposables.clear(); 
// Using dispose will clear all and set isDisposed = true, so it will not accept any new disposable
disposables.dispose(); 
```





























