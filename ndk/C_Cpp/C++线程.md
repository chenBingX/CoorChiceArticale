
# 线程基本操作
## pthread_t
定义一个线程句柄。long类型的哦！

```
pthread_t new_thread
```

## pthread_create()
创建并启动线程。
```
pthread_create(pthread_t*, pthread_attr_t const*, void* (*run) (void*), void*)
```
- 第一个参数需要一个线程句柄指针，线程创建后会给这个指针赋值，以后就可以通过这个指针操作线程了。
- 第二个参数为线程属性，NULL就行了。
- 第三个参数是一个函数指针，线程创建后会调用这个函数。
- 第四个参数为函数的参数。

```
pthread_create(&new_thread, NULL, funcation, arg);

void* funcation(void* p){
    
}
```

## pthread_exit()和pthread_join()
- pthread_exit()，退出本线程，释放资源。
- pthread_join()，退出指定线程，释放资源，调用线程将会被阻塞，直到指定线程完全退出。

# 锁和同步

##  互斥锁
互斥锁。一个锁只能被一个线程锁定，当其它线程还想锁定时，会被阻塞。直到该锁被释放。


```
//互斥锁指针
pthread_mutex_t* mutex_lock;
//初始化互斥锁
pthread_mutex_init(mutex_lock, NULL);
//锁定
pthread_mutex_lock(mutex_lock);

...

//解锁
pthread_mutex_unlock(mutex_lock);

//销毁锁
pthread_mutex_destory(mutex_lock);
```

## 条件变量
条件变量可用于线程的等待。当线程进入等待后，需要其它线程将条件变量设置为成立，并发送信号通知等待线程。

### 等待
```
//条件变量指针
pthread_cond_t* cond;
//初始化条件变量指针
pthread_cond_init(cond, NULL);

//进入等待前，必须加锁
pthread_mutex_lock(mutex_lock);
//让线程进入等待条件变量的成立的状态
//在线程真正开始等待的前一刻，会自动给mutex解锁
//当线程唤醒后，mutex又会立即被加锁。所以需要注意对mutex的操作。
pthread_cond_wait(cond, mutex);
```

### 唤醒

```
//发送唤醒信号前，必须加锁
pthread_mutex_lock(mutex_lock);
//发送唤醒信号
//唤醒信号会按照等待的顺序唤醒线程
pthread_cond_signal(cond);
//发送唤醒后需要立即解锁，否则被唤醒的线程将会阻塞在pthread_cond_wait()中
pthread_mutex_unlock(mutex_lock);
//销毁条件变量
pthread_cond_destory(cond);
```

- `pthread_cond_broadcast(cond)`，能够唤醒所有线程


