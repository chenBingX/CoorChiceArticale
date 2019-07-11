![image](http://upload-images.jianshu.io/upload_images/1869462-5d8fb59add3a9886.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 背景介绍
我们在Android开发过程中，几乎都离不开线程。但是你对线程的了解有多少呢？它完美运行的背后，究竟隐藏了多少不为人知的秘密呢？线程间互通暗语，传递信息究竟是如何做到的呢？Looper、Handler、MessageQueue究竟在这背后进行了怎样的运作。本期，让我们一起从Thread开始，逐步探寻这个完美的线程链背后的秘密。  
注意，大部分分析在代码中，所以请仔细关注代码哦！

# 从Tread的创建流程开始
在这一个环节，我们将一起一步步的分析Thread的创建流程。  
话不多说，直接代码里看。
## 线程创建的起始点init()
```
// 创建Thread的公有构造函数，都调用的都是这个私有的init()方法。我们看看到底干什么了。
/**
     *
     * @param 线程组
     * @param 就是我们平时接触最多的Runnable同学
     * @param 指定线程的名称
     * @param 指定线程堆栈的大小
     */
private void init(ThreadGroup g, Runnable target, String name, long stackSize) {
        // 获取当前正在运行的线程
        // 当前正在运行的线程就是该我们要创建的线程的父线程
        // 我们要创建的线程会从父线程那继承一些参数过来
        // 注意哦，此时仍然是在原来的线程，新线程此时还没有创建的哦！
        Thread parent = currentThread();  
        if (g == null) {
            g = parent.getThreadGroup();            //如果没有指定ThreadGroup，将获取父线程的TreadGroup
        }

        g.addUnstarted();                           //将ThreadGroup中的就绪线程计数器增加一。注意，此时线程还并没有被真正加入到ThreadGroup中。
        this.group = g;                             //将Thread实例的group赋值。从这里开始线程就拥有ThreadGroup了。

        this.target = target;                       //给Thread实例设置Runnable。以后start()的时候执行的就是它了。
        this.priority = parent.getPriority();       //设置线程的优先权重为父线程的权重
        this.daemon = parent.isDaemon();            //根据父线程是否是守护线程来确定Thread实例是否是守护线程。
        setName(name);                              //设置线程的名称  

        init2(parent);                              //纳尼？又一个初始化，参数还是父线程。不急，稍后在看。

        /* Stash the specified stack size in case the VM cares */
        this.stackSize = stackSize;                 //设置线程的堆栈大小
        tid = nextThreadID();                       //线程的id。这是个静态变量，调用这个方法会自增，然后作为线程的id。
    }
```
在Thread的init()方法中，比较重要的是会通过一个`currentThread()`这样的native函数通过底层从虚拟机中获取到当前运行的线程。  

所以在Thread初始化的时候，仍然是在创建它的线程中。不难猜测出，其实Java层的Thread只是对底层的封装而已。

## 第二个init2()
```
private void init2(Thread parent) {
        this.contextClassLoader = parent.getContextClassLoader();           //设置ClassLoader成员变量
        this.inheritedAccessControlContext = AccessController.getContext(); //设置访问权限控制环境
        if (parent.inheritableThreadLocals != null) {
            this.inheritableThreadLocals = ThreadLocal.createInheritedMap(  //创建Thread实例的ThreadLoacaleMap。需要用到父线程的ThreadLocaleMap，目的是为了将父线程中的变量副本拷贝一份到当前线程中。
            //ThreadLocaleMap是一个Entry型的数组，Thread实例会将变量副本保存在这里面。
                    parent.inheritableThreadLocals);        
        }
    }
```
至此，我们的Thread就初始化完成了，Thread的几个重要成员变量都赋值了。

# 启动线程，开车啦！
通常，我们这样了启动一条线程。
```
Thread threadDemo = new Thread(() -> {

    });
threadDemo.start();
```
那么start()背后究竟隐藏着什么样不可告人的秘密呢？是人性的扭曲？还是道德的沦丧？让我们一起点进start()。探寻start()背后的秘密。  
```
//如我们所见，这个方法是加了锁的。原因是避免开发者在其它线程调用同一个Thread实例的这个方法，从而尽量避免抛出异常。
//这个方法之所以能够执行我们传入的Runnable里的run()方法，是应为JVM调用了Thread实例的run()方法。
public synchronized void start() {
        //检查线程状态是否为0，为0表示是一个新状态，即还没被start()过。不为0就抛出异常。
        //就是说，我们一个Thread实例，我们只能调用一次start()方法。
        if (threadStatus != 0)
            throw new IllegalThreadStateException();

        //从这里开始才真正的线程加入到ThreadGroup组里。再重复一次，前面只是把nUnstartedThreads这个计数器进行了增量，并没有添加线程。
        //同时，当线程启动了之后，nUnstartedThreads计数器会-1。因为就绪状态的线程少了一条啊！
        group.add(this);

        started = false;
        try {
            //又是个Native方法。这里交由JVM处理，会调用Thread实例的run()方法。
            nativeCreate(this, stackSize, daemon);  
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);  //如果没有被启动成功，Thread将会被移除ThreadGroup，同时，nUnstartedThreads计数器又增量1了。
                }
            } catch (Throwable ignore) {
               
            }
        }
    }
```
好把，最精华的函数是`nativeCreate(this, stackSize, daemon)`，会去调用底层的JNI函数`Thread_nativeCreate()`，进一步的会调用底层的Thread类的`Thread::CreateNativeThread()`函数。  

`Thread::CreateNativeThread()`函数在`/art/runtime/thread.cc`文件中（注：CoorChice用的是6.0.0-r1的源码）。它会在去创建一个c/c++层的Thread对象，并且会关联Java层的Thread对象（其实就是保存一个Java层Thread对象的引用而已）。接着，会通过c/c++层的`pthread_create()`函数去创建并启动一个新线程。这条代码必须要看看了：

```
pthread_create_result = pthread_create(&new_pthread, &attr, 
    Thread::CreateCallback, child_thread);
```
这里我们需要注意第三个参数位置的`Thread::CreateCallback`，它会返回一个Java层Thread类的run()方法指针，在Linux层的pthread线程创建成功后，将会调用这个run()方法。这就是为什么我们调用start()方法后，run()方法会被调用的原因。  

从上面的分析我们可以知道，其实Java的线程Thread还是用的Linux那一套 `pthread` 的东西，并且一条线程真正创建并运行在虚拟机中时，是在调用start()方法之后。所以，如果你创建了一条线程，但是从没调用过它的start()方法，就不会有条新线程生成，此时的Thread对象和主线程里的一个普通对象没什么区别。如果你企图调用 `run()` 方法去试图启动你的线程，那真是大错特错了！这样不过相当于在主线程中调用了一个Java方法而已。  

所以，Java中的线程在Android中实际上走的还是Linux的pthread那一套。

```
//没错，就是这么简单！仅仅调用了Runnable类型的成员变量target的run()方法。至此，我们需要执行的代码就执行起来了。
//至于这个@Overrid的存在，完全是因为Thread本身也是一个Runnable！就是说，我们的Thread也可以作为一个Runnable来使用。
@Override
public void run() {
        if (target != null) {
            target.run();
        }
    }
```
看，如果不调用start()方法，你可以把Thread当作一个Handler去使用！！

```
public void test_1() {
    Thread thread1 = new Thread(() -> {
      System.out.println(Thread.currentThread().getName());
    }, "Thread_1");


    Thread thread2 = new Thread(thread1, "Thread_2");
    thread2.start();
  }
  

---
输出：
Thread_2
```


# 几个常见的线程手段(操作)
## Thread.sleep()那不可告人的秘密
我们平时使用Thread.sleep()的频率也比较高，所以我们在一起研究研究Thread.sleep()被调用的时候发生了什么。  
在开始之前，先介绍一个概念——纳秒。1纳秒=十亿分之一秒。可见用它计时将会非常的精准。但是由于设备限制，这个值有时候并不是那么准确，但还是比毫秒的控制粒度小很多。
```
//平时我们调用的Thread.sleep(long)最后调用到这个方法来，后一个陌生一点的参数就是纳秒。
//你可以在纳秒级控制线程。
public static void sleep(long millis, int nanos)
    throws InterruptedException {
        //下面三个检测毫秒和纳秒的设置是否合法。
        if (millis < 0) {
            throw new IllegalArgumentException("millis < 0: " + millis);
        }
        if (nanos < 0) {
            throw new IllegalArgumentException("nanos < 0: " + nanos);
        }
        if (nanos > 999999) {
            throw new IllegalArgumentException("nanos > 999999: " + nanos);
        }

    
        if (millis == 0 && nanos == 0) {
            if (Thread.interrupted()) {   //当睡眠时间为0时，检测线程是否中断，并清除线程的中断状态标记。这是个Native的方法。
              throw new InterruptedException();  //如果线程被设置了中断状态为true了(调用Thread.interrupt())。那么他将抛出异常。如果在catch住这个异常之后return线程，那么线程就停止了。  
              //需要注意，在调用了Thread.sleep()之后，再调用isInterrupted()得到的结果永远是False。别忘了Thread.interrupted()在检测的同时还会清除标记位置哦！
            }
            return;
        }

        long start = System.nanoTime();  //类似System.currentTimeMillis()。但是获取的是纳秒，可能不准。
        long duration = (millis * NANOS_PER_MILLI) + nanos;  

        Object lock = currentThread().lock;  //获得当前线程的锁。

        synchronized (lock) {   //对当前线程的锁对象进行同步操作
            while (true) {
                sleep(lock, millis, nanos);  //这里又是一个Native的方法，并且也会抛出InterruptedException异常。
                //据我估计，调用这个函数睡眠的时长是不确定的。

                long now = System.nanoTime();
                long elapsed = now - start;  //计算线程睡了多久了

                if (elapsed >= duration) {   //如果当前睡眠时长，已经满足我们的需求，就退出循环，睡眠结束。
                    break;
                }

                duration -= elapsed;   //减去已经睡眠的时间，重新计算需要睡眠的时长。
                start = now;
                millis = duration / NANOS_PER_MILLI;  //重新计算毫秒部分
                nanos = (int) (duration % NANOS_PER_MILLI); //重新计算微秒部分
            }
        }
    }
```
通过上面的分析可以知道，使线程休眠的核心方法就是一个Native函数sleep(lock, millis, nanos)。这个`sleep()`对应底层的一个JNI函数，这个JNI函数最终会调用到c/c++中对应的Thread的条件变量的 `TimedWait()`函数。这个条件变量是应该是Android中自己定义的条件变量，当然，这里的`TimedWait()`函数自然也是Android自己实现的。在这个函数里，Android直接使用了Linux的`futex()`函数。这个`futex()`函数会调用`syscall()`函数，通过一种名为【快速用户区互斥锁】的锁去执行锁定的。`futex()`的效率比`phtread_cond_wait()`要高很多。  

Android为了确保休眠的准确性，在这里还使用了一个`while()`循环，在每次线程从底层被唤醒后，检查一下是否休眠够了足够的时长。如果不够就让它继续休眠。  


同时，需要注意一点，如果线程的interruted状态在调用sleep()方法时被设置为true，那么在开始休眠循环前会抛出InterruptedException异常。

## Thread.yield()究竟隐藏了什么？
这个方法是Native的。调用这个方法可以提示cpu，当前线程将放弃目前cpu的使用权，和其它线程重新一起争夺新的cpu使用权限。当前线程可能再次获得执行，也可能没获得。就酱。    

## 无处不在的wait()究竟是什么？
大家一定经常见到，不论是哪一个对象的实例，都会在最下面出现几个名为wait()的方法。等待？它们究竟是怎样的一种存在，让我们一起点击去看看。  
哎哟我去，都是Native函数啊。  
![image](http://upload-images.jianshu.io/upload_images/1869462-fca8c85d7dcd3aab.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
那就看看文档它到底是什么吧。  
根据文档的描述，wait()配合notify()和notifyAll()能够实现线程间通讯，即同步。在线程中调用wait()必须在同步代码块中调用，否则会抛出IllegalMonitorStateException异常。因为wait()函数需要释放相应对象的锁。当线程执行到wait()时，对象会把当前线程放入自己的线程池中，并且释放锁，然后阻塞在这个地方。直到该对象调用了notify()或者notifyAll()后，该线程才能重新获得，或者有可能获得对象的锁，然后继续执行后面的语句。  
呃。。。好吧，在说明一下notify()和notifyAll()的区别。
- notify()  
调用notify()后，对象会从自己的线程池中(也就是对该对象调用了wait()函数的线程)随机挑选一条线程去唤醒它。也就是一次只能唤醒一条线程。如果在多线程情况下，只调用一次notify()，那么只有一条线程能被唤醒，其它线程会一直在
- notifyAll()  
调用notifyAll()后，对象会唤醒自己的线程池中的所有线程，然后这些线程就会一起抢夺对象的锁。  

# 扒一扒Looper、Handler、MessageQueue之间的爱恨情仇
我们可能过去都写过形如这样的代码：
```
new Thread(()->{

    ...
    Looper.prepare();
    Handler handler = new Handler(){
        @Override
        public void handleMessage(Message msg) {
          super.handleMessage(msg);
        }
      };
    Looper.loop();

}).start()
```
很多同学知道，在线程中使用Handler时(除了Android主线程)必须把它放在Looper.prepare()和Looper.loop()之间。否则会抛出RuntimeException异常。但是为什么要这么做呢？下面我们一起来扒一扒这其中的内幕。
  
![image](http://upload-images.jianshu.io/upload_images/1869462-1cb13e679f97121d.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 从Looper.prepare()开始
当Looper.prepare()被调用时，发生了什么？  
```
public static void prepare() {
        prepare(true);  //最终其实执行的是私有方法prepare(boolean quitAllowed)中的逻辑
    }

    private static void prepare(boolean quitAllowed) {
        if (sThreadLocal.get() != null) {   //先尝试获取是否已经存在一个Looper在当前线程中，如果有就抛个异常。
        //这就是为什么我们不能在一个Thread中调用两次Looper.prepare()的原因。
            throw new RuntimeException("Only one Looper may be created per thread");
        }
        sThreadLocal.set(new Looper(quitAllowed));  //首次调用的话，就创建一个新的Looper。
    }
    
    //Looper的私有构造函数
    private Looper(boolean quitAllowed) {
        mQueue = new MessageQueue(quitAllowed);   //创建新的MessageQueue，稍后在来扒它。
        mThread = Thread.currentThread();         //把当前的线程赋值给mThread。
    }
```
经过上面的分析，我们已经知道Looper.prepare()调用之后发生了什么。  
但是问题来了！sThreadLocal是个静态的**ThreadLocal<Looper>** 实例(在Android中ThreadLocal的范型固定为Looper)。那么，Looper.prepare()既然是个静态方法，Looper是如何确定现在应该和哪一个线程建立绑定关系的呢？我们接着往里扒。  
来看看ThreadLocal的get()、set()方法。  
```
public void set(T value) {
        Thread t = Thread.currentThread();  //同样先获取到当前的线程
        ThreadLocalMap map = getMap(t);     //获取线程的ThreadLocalMap
        if (map != null)
            map.set(this, value);           //储存键值对
        else
            createMap(t, value);
    }    

public T get() {
        Thread t = Thread.currentThread();   //重点啊！获取到了当前运行的线程。
        ThreadLocalMap map = getMap(t);      //取出当前线程的ThreadLocalMap。这个东西是个重点，前面已经提到过。忘了的同学在前面再看看。
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);  
            //可以看出，每条线程的ThreadLocalMap中都有一个<ThreadLocal,Looper>键值对。绑定关系就是通过这个键值对建立的。
            if (e != null)
                return (T)e.value;
        }
        return setInitialValue();
    }
```
ThreadLocal是Looper类中的静态常量，所以它对所有线程来说都是可见的。从上面代码也可以看出，调用ThreadLocal的set/get方法，实际操作的是Thread的ThreadLocalMap，也就是说每个Thread的ThreadLocalMap是Thread私有的。这样的设计，使得即使在并发的情况下，每个线程都invoke ThreadLocal的get/set方法，但是由于每个线程实际操作的都是自己的ThreadLocalMap，互不影响，所以是线程安全的。关于线程的内存你可以在CoorChice的这两篇文章中找到点线索：[关于线程，还有这些是你需要知道的！： http://www.jianshu.com/p/0d9b66827271](http://www.jianshu.com/p/0d9b66827271)， [这些是你需要知道的Android内存基础： http://www.jianshu.com/p/54241ca3da5c](http://www.jianshu.com/p/54241ca3da5c)。  

##### 思考一下：即然TheadLocalMap是每个线程自己持有的，为什么每次使用的是时候不直接取得Thread之后，然后再取得它的ThreadLocalMap来操作，而是要通过ThreadLocal去间接的操作呢？   

这样设计主要还是为了将逻辑分离出去，因为实现方案可能会改变。如果以后修改了ThreadLocalMap的管理逻辑，只要接口功能没变，Looper和Thread就不会受到影响。而这套逻辑也可以直接拿来给其它方案使用。便于修改，便于复用。


## 创建Handler
Handler可以用来实现线程间的通行。在Android中我们在子线程作完数据处理工作时，就常常需要通过Handler来通知主线程更新UI。平时我们都使用`new Handler()`来在一个线程中创建Handler实例，但是它是如何知道自己应该处理那个线程的任务呢。下面就一起扒一扒Handler。  
```
public Handler() {
        this(null, false); 
}
    
public Handler(Callback callback, boolean async) {      //可以看到，最终调用了这个方法。
        if (FIND_POTENTIAL_LEAKS) {
            final Class<? extends Handler> klass = getClass();
            if ((klass.isAnonymousClass() || klass.isMemberClass() || klass.isLocalClass()) &&
                    (klass.getModifiers() & Modifier.STATIC) == 0) {
                Log.w(TAG, "The following Handler class should be static or leaks might occur: " +
                    klass.getCanonicalName());
            }
        }

        mLooper = Looper.myLooper();                    //重点啊！在这里Handler和当前Thread的Looper绑定了。Looper.myLooper()就是从ThreadLocale中取出当前线程的Looper。
        if (mLooper == null) {
            //如果子线程中new Handler()之前没有调用Looper.prepare()，那么当前线程的Looper就还没创建。就会抛出这个异常。
            throw new RuntimeException(
                "Can't create handler inside thread that has not called Looper.prepare()");
        }
        mQueue = mLooper.mQueue;  //赋值Looper的MessageQueue给Handler。
        mCallback = callback;
        mAsynchronous = async;
    }

```

## Looper.loop()
我们都知道，在Handler创建之后，还需要调用一下Looper.loop()，不然发送消息到Handler没有用！接下来，扒一扒Looper究竟有什么样的魔力，能够把消息准确的送到Handler中处理。
```
public static void loop() {
        final Looper me = myLooper();   //这个方法前面已经提到过了，就是获取到当前线程中的Looper对象。
        if (me == null) { 
            //没有Looper.prepare()是要报错的！
            throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
        }
        final MessageQueue queue = me.mQueue;       //获取到Looper的MessageQueue成员变量，这是在Looper创建的时候new的。

        //这是个Native方法，作用就是检测一下当前线程是否属于当前进程。并且会持续跟踪其真实的身份。
        //在IPC机制中，这个方法用来清除IPCThreadState的pid和uid信息。并且返回一个身份，便于使用restoreCallingIdentity()来恢复。
        Binder.clearCallingIdentity();
        final long ident = Binder.clearCallingIdentity();

        for (;;) {  //重点(敲黑板)！这里是个死循环，一直等待抽取消息、发送消息。
            Message msg = queue.next(); //  从MessageQueue中抽取一条消息。至于怎么取的，我们稍后再看。
            if (msg == null) {
                // No message indicates that the message queue is quitting.
                return;
            }

            // This must be in a local variable, in case a UI event sets the logger
            final Printer logging = me.mLogging;
            if (logging != null) {
                logging.println(">>>>> Dispatching to " + msg.target + " " +
                        msg.callback + ": " + msg.what);
            }

            final long traceTag = me.mTraceTag;   //取得MessageQueue的跟踪标记
            if (traceTag != 0) {
                Trace.traceBegin(traceTag, msg.target.getTraceName(msg));  //开始跟踪本线程的MessageQueue中的当前消息，是Native的方法。
            }
            try {
                msg.target.dispatchMessage(msg);   //尝试分派消息到和Message绑定的Handler中
            } finally {
                if (traceTag != 0) {
                    Trace.traceEnd(traceTag);      //这个和Trace.traceBegin()配套使用。
                }
            }

            if (logging != null) {
                logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);
            }

            
            final long newIdent = Binder.clearCallingIdentity();   //what？又调用这个Native方法了。这里主要是为了再次验证，线程所在的进程是否发生改变。
            if (ident != newIdent) {
                Log.wtf(TAG, "Thread identity changed from 0x"
                        + Long.toHexString(ident) + " to 0x"
                        + Long.toHexString(newIdent) + " while dispatching to "
                        + msg.target.getClass().getName() + " "
                        + msg.callback + " what=" + msg.what);
            }

            msg.recycleUnchecked();   //回收释放消息。
        }
    }
```
从上面的分析可以知道，当调用了Looper.loop()之后，线程就就会被一个for(;;)死循环阻塞，每次等待MessageQueue的next()方法取出一条Message才开始往下继续执行。然后通过Message获取到相应的Handler (就是target成员变量)，Handler再通过dispatchMessage()方法，把Message派发到handleMessage()中处理。  
  
  
这里需要注意，当线程loop起来是时，线程就一直在循环中。就是说Looper.loop()后面的代码就不能被执行了。想要执行，需要先退出loop。
```
Looper myLooper = Looper.myLoop();
myLooper.quit();        //普通退出方式。
myLooper.quitSafely();  //安全的退出方式。  
```
  
  
现在又产生一个疑问，MessageQueue的next()方法是如何阻塞住线程的呢？接下来，扒一扒这个幕后黑手MessageQueue。   
## 幕后黑手MessageQueue
MessageQueue是一个用单链的数据结构来维护消息列表。
```
Message next() {
        //检查loop是否已经为退出状态。mPrt是Native层的MessageQueue的地址。通过这个地址可以和Native层的MessageQueue互动。
        final long ptr = mPtr;
        if (ptr == 0) {
            return null;
        }

        int pendingIdleHandlerCount = -1;
        int nextPollTimeoutMillis = 0;      //时间标记，当且仅当第一次获取消息时才为0。因为它在死循环外面啊！
        for (;;) {
            if (nextPollTimeoutMillis != 0) {
                Binder.flushPendingCommands();     
                //如果不是第一次获取消息，调用Native的函数，让虚拟机刷新所有的饿Binder命令，确保进程在执行可能阻塞的任务之前，释放之前的对象。
            }

            //这是一个Native的方法。
            nativePollOnce(ptr, nextPollTimeoutMillis);

            synchronized (this) {       //锁住MessageQueue
                //获取当前的系统时间，用于后面和msg.when进行比较。
                final long now = SystemClock.uptimeMillis();
                Message prevMsg = null;
                Message msg = mMessages;        //获得当前MessageQueue中的第一条消息
                if (msg != null && msg.target == null) {
                
                    do {
                        prevMsg = msg;
                        msg = msg.next;
                    } while (msg != null && !msg.isAsynchronous());
                }
                if (msg != null) {
                    if (now < msg.when) {  //这个判断的意义在于只有到了Message应该被发送的时刻才去发送，否则继续循环。
                        //计算下一条消息的时间。注意最大就是Integer.MAX_VALUE。
                        nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);
                    } else {  //应该发送一条消息了。
                        // Got a message.
                        mBlocked = false;
                        if (prevMsg != null) {
                            prevMsg.next = msg.next;
                        } else {
                            mMessages = msg.next;
                        }
                        msg.next = null;
                        if (DEBUG) Log.v(TAG, "Returning message: " + msg);
                        msg.markInUse();   //转换消息标记为使用过的
                        return msg;         //返回一条消息给Looper。
                    }
                } else {
                    // 如果取到的Message为null，将时间标记设置为-1。
                    nextPollTimeoutMillis = -1;
                }

                // Process the quit message now that all pending messages have been handled.
                if (mQuitting) {
                    dispose();
                    return null;
                }

                // If first time idle, then get the number of idlers to run.
                // Idle handles only run if the queue is empty or if the first message
                // in the queue (possibly a barrier) is due to be handled in the future.
                if (pendingIdleHandlerCount < 0
                        && (mMessages == null || now < mMessages.when)) {
                    pendingIdleHandlerCount = mIdleHandlers.size();
                }
                if (pendingIdleHandlerCount <= 0) {
                    // No idle handlers to run.  Loop and wait some more.
                    mBlocked = true;
                    continue;
                }

                if (mPendingIdleHandlers == null) {
                    mPendingIdleHandlers = new IdleHandler[Math.max(pendingIdleHandlerCount, 4)];
                }
                mPendingIdleHandlers = mIdleHandlers.toArray(mPendingIdleHandlers);
            }

            // Run the idle handlers.
            // We only ever reach this code block during the first iteration.
            for (int i = 0; i < pendingIdleHandlerCount; i++) {
                final IdleHandler idler = mPendingIdleHandlers[i];
                mPendingIdleHandlers[i] = null; // release the reference to the handler

                boolean keep = false;
                try {
                    keep = idler.queueIdle();
                } catch (Throwable t) {
                    Log.wtf(TAG, "IdleHandler threw exception", t);
                }

                if (!keep) {
                    synchronized (this) {
                        mIdleHandlers.remove(idler);
                    }
                }
            }

            // Reset the idle handler count to 0 so we do not run them again.
            pendingIdleHandlerCount = 0;

            // While calling an idle handler, a new message could have been delivered
            // so go back and look again for a pending message without waiting.
            nextPollTimeoutMillis = 0;
        }
    }
```
可以看到。MessageQueue在取消息(调用next())时，会进入一个死循环，直到取出一条Message返回。这就是为什么Looper.loop()会在queue.next()处等待的原因。   

在这个方法中需要注意一个参数`mPtr`，它是底层的MessageQueue对象的地址。就是说Android的c/c++层也有一套与Java层对应的Handler机制，而我们的MessageQueue由于持有了一个底层的引用，自然就成了Java层的Handler机制和底层的沟通桥梁了。  

上面方法中出现了一个`nativePollOnce(ptr, nextPollTimeoutMillis);`函数的调用。线程会被阻塞在这个地方。这个native方法会调用到底层的JNI函数`android_os_MessageQueue_nativePollOnce()`，进一步调用c/c++层的`nativeMessageQueue`的`pollOnce()`函数，在这个函数中又会通过本线程在底层的Looper的`pollOnce()`函数，进而调用`pollInner()`函数。在`pollInner()`函数中会调用`epoll_wait()`函数，这个函数会将线程阻塞在这，直到被超时或者检测到pipe中有事件发生。那么阻塞在这怎么唤醒呢，我们下面在说。
  
那么，一条Message是如何添加到MessageQueue中呢？要弄明白最后的真相，我们需要调查一下mHandler.post()这个方法。

## Handler究竟对Message做了什么？

Handler的post()系列方法，最终调用的都是下面这个方法：
```
private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        msg.target = this;      //在这里给Message的target赋值。
        if (mAsynchronous) {
            msg.setAsynchronous(true);      //如果是异步，就标记为异步
        }
        return queue.enqueueMessage(msg, uptimeMillis);     //就是这个方法把Message添加到线程的MessageQueue中的。
    }
```
接下来就看看MessageQueue的enqueueMessage()作了什么。
```
boolean enqueueMessage(Message msg, long when) {
        if (msg.target == null) {   //没Handler调用是会抛异常的啊
            throw new IllegalArgumentException("Message must have a target.");
        }
        if (msg.isInUse()) {        //不能使用一条正在使用中的Message。
            throw new IllegalStateException(msg + " This message is already in use.");
        }

        synchronized (this) {       //锁住MessageQueue再往里添加消息。
            if (mQuitting) {        //如果MessageQueue被标记为退出，就返回。
                IllegalStateException e = new IllegalStateException(
                        msg.target + " sending message to a Handler on a dead thread");
                Log.w(TAG, e.getMessage(), e);
                msg.recycle();
                return false;
            }

            msg.markInUse();        //切换Message的使用状态为未使用。
            msg.when = when;        //我们设置的延迟发送的时间。
            //经过下面的逻辑，Message将会被“储存”在MessageQueue中。实际上，Message在MessageQueue中的储存方式，
            //是使用Message.next逐个向后指向的单链表结构来储存的。比如：A.next = B, B.next = C...
            Message p = mMessages;  //尝试获取当前Message
            boolean needWake;
            if (p == null || when == 0 || when < p.when) {
                // 如果为null，说明是第一条。
                msg.next = p;   
                mMessages = msg;    //设置当前的Message为传入的Message，也就是作为第一条。
                needWake = mBlocked;
            } else {
            
                needWake = mBlocked && p.target == null && msg.isAsynchronous();
                Message prev;
                //不满足作为第一条Message的条件时，通过下面的逐步变换，将它放在最后面。这样便把Message“储存”到MessageQueue中了。
                for (;;) {
                    prev = p;
                    p = p.next;
                    if (p == null || when < p.when) {
                        break;
                    }
                    if (needWake && p.isAsynchronous()) {
                        needWake = false;
                    }
                }
                msg.next = p; 
                prev.next = msg;
            }

          
            if (needWake) {
                nativeWake(mPtr);
            }
        }
        return true;
    }
```
上一节面CoorChice说过，MessageQueue在`next()`方法中会阻塞在`nativePollOnce()`这个地方，实际上是阻塞在了底层的Looper的`epoll_wait()`这个地方等待唤醒呢。看到上面这段代码的最后面没？`nativeWake()`，赤裸裸的表明就是唤醒。实际上这个`nativeWake()`函数表明pipe写端有write事件发生，从而让`epoll_wait()`退出等待。  


至此，我们已经揭露了Looper、Handler、MessageQueue隐藏的秘密。   

# 另一个疑问？

也许你已经注意到在主线程中可以直接使用Handler，而不需要Looper.prepare()和Looper.loop()。为什么可以做到这样呢？根据之前的分析可以知道，主线程中必然存在Looper.prepare()和Looper.loop()。既然如此，为什么主线程没有被loop()阻塞呢？看一下ActivityThread来弄清楚到底是怎么回事。
```
//这个main()方法可以认为是Android应用的起点
public static void main(String[] args) {
                。
                。
                。
        Looper.prepareMainLooper();                             //主要作用和我们平时调用的Looper.prepare()差不多

        ActivityThread thread = new ActivityThread();           //创建本类实例
        thread.attach(false);

        if (sMainThreadHandler == null) {
            sMainThreadHandler = thread.getHandler();           //重点啊！这里取得了处理主线程事物的Handler。
        }

        if (false) {
            Looper.myLooper().setMessageLogging(new
                    LogPrinter(Log.DEBUG, "ActivityThread"));
        }

        // End of event ActivityThreadMain.
        Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
        Looper.loop();                                          //开始循环。可以看到，主线程本质上是阻塞的！
                。
                。
                。
        }
```
注意ActivityThread并没有继承Thread，它的Handler是继承Handler的私有内部类H.class。在H.class的handleMessage()中，它接受并执行主线程中的各种生命周期状态消息。UI的16ms的绘制也是通过Handler来实现的。也就是说，主线程中的所有操作都是在Looper.prepareMainLooper()和Looper.loop()之间进行的。进一步说是在主Handler中进行的。

# 总结

1. Android中Thread在创建时进行初始化，会使用当前线程作为父线程，并继承它的一些配置。
2. Thread初始化时会被添加到指定/父线程的ThreadGroup中进行管理。
3. Thread正真启动是一个native函数完成的。
4. 在Android的线程间通信中，需要先创建Looper，就是调用Looper.prepare()。这个过程中会自动依赖当前Thread，并且创建MessageQueue。经过上一步，就可以创建Handler了，默认情况下，Handler会自动依赖当前线程的Looper，从而依赖相应的MessageQueue，也就知道该把消息放在哪个地方了。MessageQueue通过Message.next实现了一个单链表结构来缓存Message。消息需要送达Handler处理，还必须调用Looper.loop()启动线程的消息泵送循环。loop()内部是无限循环，阻塞在MessageQueue的next()方法上，因为next()方法内部也是一个无限循环，直到成功从链表中抽取一条消息返回为止。然后，在loop()方法中继续进行处理，主要就是把消息派送到目标Handler中。接着进入下一次循环，等待下一条消息。由于这个机制，线程就相当于阻塞在loop()这了。  
  
经过上面的揭露，我们已经对线程及其相互之间通讯的秘密有所了解。掌握了这些以后，相信在以后的开发过程中我们可以思路清晰的进行线程的使用，并且能够吸收Android在设计过程中的精华思想。  

感觉不错就关注我，都不干就点个赞！😘
