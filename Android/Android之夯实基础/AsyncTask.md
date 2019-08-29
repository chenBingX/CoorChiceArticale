

# 1. AsyncTask的四个回调

## 1.1 onPreExecute
在主线程中执行

## 1.2 doInBackground
在线程池中执行

## 1.3 onProgressUpdate
在主线程中执行

## 1.4 onPostExecute
在主线程中执行

# 2. AsyncTask的版本变化
1. 在Android 1.6之前采用串行
2. Android1.6 ~ Android3.0，采用线程池并行执行
3. Android3.0 之后，采用串行执行。但是仍然可以通过executeOnExecutor()来并行执行。


# 3. AsyncTask源码分析

## 3.1 AsyncTask创建
AsyncTask创建时会WorkerRunnable，doInBackgroud()就是在它的call中调用的。  
```
mWorker = new WorkerRunnable<Params, Result>() {
    public Result call() throws Exception {
        mTaskInvoked.set(true);
        Result result = null;
        try {
            Process.setThreadPriority(Process.THREAD_PRIORITY_BACKGROUND);
            // 执行
            result = doInBackground(mParams);
            Binder.flushPendingCommands();
        } catch (Throwable tr) {
            mCancelled.set(true);
            throw tr;
        } finally {
            // 回调主线程
            postResult(result);
        }
        return result;
    }
};
```
接着，把WorkerRunnable封装到FutureTask中，通过FutrueTask调用WorkerRunnable中的call，进而执行doInBackground()。


## 3.2 execute调用链

```
- execute()
  - executeOnExecutor()
    - onPreExecute()
    - SerialExecutor::execute(): 这是一个加锁的方法。
       - ArrayDeque<Runnable>::offer()： 将任务加入到任务队列中
       - SerialExecutor::scheduleNext(): 取出一个任务执行
         - ArrayDeque<Runnable>::poll()：取出一个任务
         - ThreadPoolExecutor::execute()
           - FutureTask::run()
             - WorkerRunnable::call()
               - doInBackground()
               - postResult()
```

## 3.3 AsyncTask的线程池
在3.0之后的AsyncTask中有一个ThreadPoolExecutor线程池，所以它也能并行执行任务。
```
static {
    ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(
            CORE_POOL_SIZE, MAXIMUM_POOL_SIZE, KEEP_ALIVE_SECONDS, TimeUnit.SECONDS,
            sPoolWorkQueue, sThreadFactory);
    threadPoolExecutor.allowCoreThreadTimeOut(true);
    THREAD_POOL_EXECUTOR = threadPoolExecutor;
}
```
看看它的任务池有多大

```
private static final BlockingQueue<Runnable> sPoolWorkQueue =
        new LinkedBlockingQueue<Runnable>(128);
```
也就是最多可以有128个任务放入线程池中。  

## 3.3.1 LinkedBlockingQueue
这是一个生产者消费者模型的队列。  

即一个线程在一直生产，直到池满了之后等待；一个线程一直在消费，等到池空了之后等待。  

这个Queue中有两个ReentrantLock，一个用于put等操作的时候上锁，一个用于poll、take等操作的时候上锁。  




### 3.3.1.1 put操作

```
public void put(E e) throws InterruptedException {
        if (e == null) throw new NullPointerException();
        // Note: convention in all put/take/etc is to preset local var

        /*注意上面这句话,约定所有的put/take操作都会预先设置本地变量,
        可以看到下面有一个将putLock赋值给了一个局部变量的操作
        */
        int c = -1;
        Node<E> node = new Node(e);
        /* 
         在这里首先获取到putLock,以及当前队列的元素数量
         即上面所描述的预设置本地变量操作
        */
        final ReentrantLock putLock = this.putLock;
        final AtomicInteger count = this.count;
        /*
            执行可中断的锁获取操作,即意味着如果线程由于获取
            锁而处于Blocked状态时，线程是可以被中断而不再继
            续等待，这也是一种避免死锁的一种方式，不会因为
            发现到死锁之后而由于无法中断线程最终只能重启应用。
        */
        putLock.lockInterruptibly();
        try {
            /*
            当队列的容量到底最大容量时,此时线程将处于等待状
            态，直到队列有空闲的位置才继续执行。使用while判
            断依旧是为了放置线程被"伪唤醒”而出现的情况,即当
            线程被唤醒时而队列的大小依旧等于capacity时，线
            程应该继续等待。
            */
            while (count.get() == capacity) {
                notFull.await();
            }
            //让元素进行队列的末尾,enqueue代码在上面分析过了
            enqueue(node);
            //首先获取原先队列中的元素个数,然后再对队列中的元素个数+1.
            c = count.getAndIncrement();
            /*注:c+1得到的结果是新元素入队列之后队列元素的总和。
            当前队列中的总元素个数小于最大容量时,此时唤醒其他执行入队列的线程
            让它们可以放入元素,如果新加入元素之后,队列的大小等于capacity，
            那么就意味着此时队列已经满了,也就没有必须要唤醒其他正在等待入队列的线程,因为唤醒它们之后，它们也还是继续等待。
            */
            if (c + 1 < capacity)
                notFull.signal();
        } finally {
            //完成对锁的释放
            putLock.unlock();
        }
        /*当c=0时，即意味着之前的队列是空队列,出队列的线程都处于等待状态，
        现在新添加了一个新的元素,即队列不再为空,因此它会唤醒正在等待获取元素的线程。
        */
        if (c == 0)
            signalNotEmpty();
    }

    /*
    唤醒正在等待获取元素的线程,告诉它们现在队列中有元素了
    */
    private void signalNotEmpty() {
        final ReentrantLock takeLock = this.takeLock;
        takeLock.lock();
        try {
            //通过notEmpty唤醒获取元素的线程
            notEmpty.signal();
        } finally {
            takeLock.unlock();
        }
    }
```

### 3.3.1.2 take
```
public E take() throws InterruptedException {
        E x;
        int c = -1;
        final AtomicInteger count = this.count;
        final ReentrantLock takeLock = this.takeLock;
        //通过takeLock获取锁，并且支持线程中断
        takeLock.lockInterruptibly();
        try {
            //当队列为空时，则让当前线程处于等待
            while (count.get() == 0) {
                notEmpty.await();
            }
            //完成元素的出队列
            x = dequeue();
            /*            
               队列元素个数完成原子化操作-1,可以看到count元素会
               在插入元素的线程和获取元素的线程进行并发修改操作。
            */
            c = count.getAndDecrement();
            /*
              当一个元素出队列之后，队列的大小依旧大于1时
              当前线程会唤醒其他执行元素出队列的线程,让它们也
              可以执行元素的获取
            */
            if (c > 1)
                notEmpty.signal();
        } finally {
            //完成锁的释放
            takeLock.unlock();
        }
        /*
            当c==capaitcy时，即在获取当前元素之前，
            队列已经满了，而此时获取元素之后，队列就会
            空出一个位置，故当前线程会唤醒执行插入操作的线
            程通知其他中的一个可以进行插入操作。
        */
        if (c == capacity)
            signalNotFull();
        return x;
    }


    /**
     * 让头部元素出队列的过程
     * 其最终的目的是让原来的head被GC回收，让其的next成为head
     * 并且新的head的item为null.
     * 因为LinkedBlockingQueue的头部具有一致性:即元素为null。
     */
    private E dequeue() {
        Node<E> h = head;
        Node<E> first = h.next;
        h.next = h; // help GC
        head = first;
        E x = first.item;
        first.item = null;
        return x;
    }

```
## 3.3.2 超时为30s

```
private static final int KEEP_ALIVE_SECONDS = 30;
```

## 3.4 AsyncTask需要注意的
### 3.4.1 AsyncTask只能在主线程中加载
因为AsyncTask依靠Handler切换线程，所以类必须在主线程加载才能使Handler在主线程创建。

### 3.4.2 AsyncTask只能在主线程中创建
因为AsyncTask中的Handler是静态的，如果AsyncTask在其它线程创建会导致Handler被竞争，产生阻塞。

### 3.4.3 AsyncTask只能调用一次
因为AsyncTask创建时会将任务包装成FutureTask，执行完后会标记，所以不能重复执行。

### 3.4.4 execute()只能在主线程中调用
否则`onPreExecute()`等就不是运行在主线程中的了。