
# 1. ReentrantLock简介
ReentrantLock是从jdk1.5开始加入的，是一个可重入的高效锁，实现了Lock，拥有一个入锁计数器，实现的效果和syncronized一样的语义。  

ReentrantLock在syncronized锁机制的基础上，实现了锁投票、定时锁和可中断锁的功能。  

使用Lock时，必须使用`try-finally`包裹起同步块，并在finally中释法锁，否则发生崩溃，锁将不会被释放。  


```
ReentrantLock lock = new ReentrantLock()

lock.lock();
try{
    // toSomething...
} finally {
    lock.unlock();
}
```

# 2. ReetrantLock的高效性
[实验证明ReentrantLock花费在线程调度上的资源更少](http://blog.csdn.net/fw0124/article/details/6672522)


# 3. Lock接口定义锁的标准操作

```
public interface Lock {

    // 获得锁
    void lock();

    // 获得一个可以被中断的锁
    void lockInterruptibly() throws InterruptedException;

    // 尝试获取锁
    boolean tryLock();

    // 尝试获取锁，并在time个unit单位后释放
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;

    // 释放锁
    void unlock();

    // 获取条件变量，类似于pthread中的条件变量
    Condition newCondition();
}
```

# 3. 条件变量Condition
和pthread的条件变量类似，Condition提供了wait休眠操作和signal唤醒操作，并且同样在wait之前需要加锁。

# 4. 公平锁 or 不公平锁？
公平锁是按照线程请求锁的顺序获得锁的。  

不公平锁允许线程比其它线程优先获得锁。