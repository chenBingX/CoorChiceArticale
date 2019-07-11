> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

[中文官方文档](https://mcxiaoke.gitbooks.io/rxdocs/content/Observables.html)  

[教程](http://gank.io/post/560e15be2dca930e00da1083)
# 一、为什么要使用响应式编程  
1.尽管响应式编程代码量比普通线程处理方式代码量大，但是响应式的突出特点就是更符合人的逻辑思维，更容易被理解。  
2.响应式编程目的是为了处理线程工作的，它有一个观察者（Observer）在随时待命，准备接收可观察对象（Observable）发送的数据。  
3.响应式编程的代码能够按照顺序一步步执行。  
# 二、预备概念
**1.Subscriber**订阅者（本质上也是一个观察者），它可以订阅一个可观察对象Observable，通常这个可观察对
		     象可能会是一个异步任务。  
**2.Observable**可观察对象，一个异步任务可作为一个可观察对象。可观察对象分为热和冷：  
		       **热**的可观察对象一创建就能够发射数据，因此订阅它的观察者Observer可能就会因为创建的比较晚而错过一些数据。  
		       **冷**的可观察对象需要等到有一个观察者观察它时，它才开始发射数据。  
		      还有一种比较特 DZ殊的Observable是connectable类型的，它只有在`connect()`方法被调用之后才会开
		      始发射数据。  
**3.Subject**这是一个十分特殊的类型，它相当于一个桥梁。可以说，它既是一个Observer，又是一个
Observable。  
**4.Scheculer**调度器，用于指定工作线程。SubscribeOn()用于指定可观察者的逻辑线程，ObserveOn()用于指定订阅者的逻辑线程。
- 当二者都没指定时，在当前线程中工作。
- 当只指定了SubscribeOn()，那么就都在这个线程中工作。
- 当指定了ObserveOn()，那么可观察者运行在默认线程，而订阅者运行在指定线程。
# 操作符
## 一、创建操作符
- create：从头开始创建可观察对象。
```
Log.e("MainActivity", " -> initData: " + Thread.currentThread().getName());
    //使用Create操作符创建可观察者
    Observable.create(new Observable.OnSubscribe<Integer>() {
      //发射前的逻辑处理
      @Override
      public void call(Subscriber<? super Integer> observer) {
        try {
          if (!observer.isUnsubscribed()) {
            for (int i = 1; i < 3; i++) {
              observer.onNext(i);
              Thread.sleep(1000);
              Log.e("MainActivity", " -> call: " + Thread.currentThread().getName());
            }
            observer.onCompleted();
          }
        } catch (Exception e) {
          observer.onError(e);
        }
      }
    } )
      //指定可观察者的逻辑线程
      //.subscribeOn(Schedulers.newThread())
      //指定订阅者的逻辑线程,没有指定则与subscribeOn()在同一个线程工作。如果两者都没指定,就在当前线程中工作。
      .observeOn(Schedulers.io())
      //可观察者发射数据
      .subscribe(
        //创建订阅者
        new Subscriber<Integer>() {
      @Override
      public void onNext(Integer item) {
        System.out.println("Next: " + item);
        Log.e("MainActivity", " -> onNext: " + Thread.currentThread().getName());
      }

      @Override
      public void onError(Throwable error) {
        System.err.println("Error: " + error.getMessage());
      }

      @Override
      public void onCompleted() {
        System.out.println("Sequence complete.");
      }
    });
```
- from：通过一个集合创建可观察对象。
```
List<String> data = new ArrayList<>();
    data.add("1");
    data.add("2");
    data.add("3");
    // 把一个集合转化为数据按顺序发射
    Observable.from(data)
        // 指定可观察者工作线程
        .subscribeOn(Schedulers.newThread())
        // 指定订阅者工作线程
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(
            s -> {
              Log.e("MainActivity", " ->Action1 call: " + s);
              Log.e("MainActivity", " -> Action1 call: thread" + Thread.currentThread());
            },
            throwable -> {
              Log.e("MainActivity", " ->Action1-Throwable call: " + throwable);
              Log.e("MainActivity", " -> Action1-Throwable call: thread" + Thread.currentThread());

            },
            () -> {
              Log.e("MainActivity", " ->Action0 call: ");
              Log.e("MainActivity", " -> Action0 call: thread" + Thread.currentThread());
            });
```
- just：同from类似，只是just的值是在()中指定的。
```
    Observable.just(1, "3", true) // 按顺序发送三种不同类型的数据
        // 订阅者的工作线程
        .observeOn(Schedulers.newThread())
        // 订阅
        .subscribe(
            // 创建订阅者,范型是个可序列化对象。
            new Subscriber<Serializable>() {
              @Override
              public void onCompleted() {

            }

              @Override
              public void onError(Throwable e) {

            }

              @Override
              public void onNext(Serializable serializable) {
                if (serializable.equals(1)) {
                  Log.e("MainActivity", " -> onNext: int = " + serializable.toString());
                }
                if (serializable.equals("3")) {
                  Log.e("MainActivity", " -> onNext: String = " + serializable.toString());
                }
                if (serializable.equals(false)) {
                  Log.e("MainActivity", " -> onNext: boolean = " + true);
                }
              }
            });
输出结果：
MainActivity:  -> onNext: int = 1
MainActivity:  -> onNext: String = 3
```


- timer：在指定时间之后创建一个发射0的可观察者。
```
Observable.timer(2, TimeUnit.SECONDS) //通过timer创建延迟2s发射的可观察者,它会发射一个0
      //指定订阅者的工作线程
      .subscribeOn(Schedulers.newThread())
      //订阅,并创建订阅者
      .subscribe(new Subscriber<Long>() {
      @Override
      public void onCompleted() {

      }

      @Override
      public void onError(Throwable e) {

      }

      @Override
      public void onNext(Long aLong) {
        Log.e("MainActivity", " -> onNext: along = " + aLong);

      }
    });
```
## 变换操作符
变换操作符可以在发射过程中对发送的数据进行加工。
-  map：可在Observable发射前对初始的数据进行一次变换，通过Func1接口来进行。

```
    Observable.just(
        "http://img.pconline.com.cn/images/upload/upc/tx/photoblog/1503/17/c2/3974265_1426551589288_mthumb.jpg")
        .map(s -> { // 在这里使用map操作符,把String类型的数据转换成了Bitmap类型,继续往下传递
          try {
            // 下载图片
            URL url = new URL(s);
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();
            conn.setRequestMethod("GET");
            conn.setDoInput(true);
            conn.setConnectTimeout(5000);
            conn.connect();
            if (conn.getResponseCode() == HttpURLConnection.HTTP_OK) {
              InputStream is = conn.getInputStream();
              byte[] bytes = new byte[1024];
              ByteArrayOutputStream byteBuffer = new ByteArrayOutputStream();
              int length = 0;
              while ((length = is.read(bytes)) != -1) {
                byteBuffer.write(bytes, 0, length);
                byteBuffer.flush();
              }
              byte[] bmpBytes = byteBuffer.toByteArray();
              Log.e("MainActivity", " -> initData: byte.size = " + bmpBytes.length);
              return BitmapFactory.decodeByteArray(bmpBytes, 0, bmpBytes.length);
            }

          } catch (MalformedURLException e) {
            e.printStackTrace();
          } catch (IOException e) {
            e.printStackTrace();
          }
          return null;
        })
        .map(BitmapDrawable::new) // 这里再一次把Bitmap类型的数据转换成了Drawable往下传递
        .subscribeOn(Schedulers.io()) // 指定可观察者工作线程
        .observeOn(AndroidSchedulers.mainThread()) // 指定订阅者的工作线程
        // 订阅
        .subscribe(
            // 创建订阅者
            new Subscriber<Drawable>() {
              @Override
              public void onCompleted() {

            }

              @Override
              public void onError(Throwable e) {

            }

              @Override
              public void onNext(Drawable drawable) {
                displayImage.setImageDrawable(drawable);
              }
            });

```
- flatMap：功能与map类似，但它返回的是一个Observable<转化类型>。
```
    APIClient.getArticles()
        // 在调用OnNext()之前先调用这个方法,这里的OnNext()是由Retrofit去调用的。
        .doOnNext(articles -> {
          Log.e("MainActivity", " -> initData: itemsSize = " + articles.items.size());
          articles.items.remove(0);
        })
        // 用上一个Observable返回的结果来创建一个新的Observable
        .flatMap(articles -> Observable.from(articles.items)) // 这里使用了from方法创建Observable
        // 指定接收者的工作线程
        .observeOn(AndroidSchedulers.mainThread())
        // 发射给订阅者
        .subscribe(
            // 创建订阅者
            new Subscriber<Articles.ItemsTestBean>() {
              @Override
              public void onCompleted() {

            }

              @Override
              public void onError(Throwable e) {

            }

              @Override
              public void onNext(Articles.ItemsTestBean itemsTestBean) {
                Log.e("MainActivity", " -> onNext: item " + i++ + " = " + itemsTestBean.id);
              }
            });
```
# 过滤操作符
- filter：通过Func1接口来实现过滤掉数据源中不符合条件的数据。
```
Observable.just(1,2,3,4,5)
    //过滤出小于4的数据，并且发射它们
    .filter(new Func1<Integer,Boolean>() {
        @Override
        public Boolean call(Integer item){
            retrun (item<4);
        }
    }).subscribe(new Subscriber<Integer> (){
         @Override
        public void onNext(Integer item) {
            System.out.println("Next: " + item);
        }

        @Override
        public void onError(Throwable error) {
            System.err.println("Error: " + error.getMessage());
        }

        @Override
        public void onCompleted() {
            System.out.println("Sequence complete.");
        }
    })；
```

- first：只发射第一条数据，或者过滤出来的第一条数据。
```
Observable.just(1,2,3,4,5)
    //过滤出大于4的数据，并且发射只发射第一个数据
    .first(new Func1<Integer,Boolean>() {
        @Override
        public Boolean call(Integer item){
            retrun (item>4);
        }
    }).subscribe(new Subscriber<Integer> (){
         @Override
        public void onNext(Integer item) {
            System.out.println("Next: " + item);
        }

        @Override
        public void onError(Throwable error) {
            System.err.println("Error: " + error.getMessage());
        }

        @Override
        public void onCompleted() {
            System.out.println("Sequence complete.");
        }
    })；
```

- take：只发射前几条数据。
```
Observable.just(1,2,3,4,5)
    //只发射前3个数据
    .take(3)
    .subscribe(new Subscriber<Integer> (){
         @Override
        public void onNext(Integer item) {
            System.out.println("Next: " + item);
        }

        @Override
        public void onError(Throwable error) {
            System.err.println("Error: " + error.getMessage());
        }

        @Override
        public void onCompleted() {
            System.out.println("Sequence complete.");
        }
    })；
```

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-65395e4f4d8c35f7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
