![image](http://upload-images.jianshu.io/upload_images/1869462-f91da9515de7510c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)    


> Binder系列第一篇:[《从getSystemService()开始，开撸Binder通讯机制》http://www.jianshu.com/p/1050ce12bc1e](http://www.jianshu.com/p/1050ce12bc1e)  

> Binder系列第二篇:[《能用【白话文】来分析Binder通讯机制？》http://www.jianshu.com/p/fe816777f2cf](http://www.jianshu.com/p/fe816777f2cf)  

> Binder系列第三篇:[《Binder机制之一次响应的故事》http://www.jianshu.com/p/4fba927dce05](http://www.jianshu.com/p/4fba927dce05)   



CoorChice在上次的文章  [《从getSystemService()开始，开撸Binder通讯机制：http://www.jianshu.com/p/1050ce12bc1e》](http://www.jianshu.com/p/1050ce12bc1e) 中留了一些关于Binder的坑，也许大家看的时候有些云里雾里的，这篇文章，CoorChice就开始填这些坑了。并开始逐步的深入Binder核心机制，让你对Android中最重要的部分有所了解。  

好了，咱们发车了！

# 由open_driver()开始
![image](http://upload-images.jianshu.io/upload_images/1869462-ff540decea8ebf42.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

在
[《从getSystemService()开始，开撸Binder通讯机制：http://www.jianshu.com/p/1050ce12bc1e》](http://www.jianshu.com/p/1050ce12bc1e)这篇文章中，相信大家应该看到在`/frameworks/native/libs/binder/ProcessState.cpp`文件中，有这样一段代码。

```
static int open_driver()
{
    //打开"/dev/binder"Binder驱动文件，并获得其描述符
    int fd = open("/dev/binder", O_RDWR);
    ...
    //获取Binder驱动程序的版本号
    status_t result = ioctl(fd, BINDER_VERSION, &vers);
    ...
    size_t maxThreads = 15;
    //告知驱动程序最多可以启动15条线程处理事物
    result = ioctl(fd, BINDER_SET_MAX_THREADS, &maxThreads);
    ...
    return fd;
}
```
它在ProcessState创建的时候会被调用。它肩负了一项重要的使命，就是在该进程中打开`/dev/binder`设备文件，然后获得该设备文件的描述符。可以看到，打开设备文件是通过`open()`函数实现的，它是怎么实现的呢？  

## 用户空间函数与Binder驱动函数
首先打开`/drivers/staging/android/binder.c`文件，然后找到下面这个结构体：
```
//这个结构体中定义了文件操作符与Binder驱动的对应函数关联
static const struct file_operations binder_fops = {
    .owner = THIS_MODULE,
    .poll = binder_poll,
    .unlocked_ioctl = binder_ioctl,
    .compat_ioctl = binder_ioctl,
    //用户空间的mmap()操作，会引起Binder驱动的binder_mmap()函数的调用
    .mmap = binder_mmap,
    //用户空间的open()操作，会引起Binder驱动的binder_open()函数的调用
    .open = binder_open, 
    .flush = binder_flush,
    .release = binder_release,
};

```
这个结构体会作为下面这个结构体的一个成员，在Binder驱动注册的时候被和Linux定义的文件操作符关联。

```
static struct miscdevice binder_miscdev = {
    .minor = MISC_DYNAMIC_MINOR,
    //定义设备节点文件名。这里Binder驱动设备的文件路径即为/dev/binder
    .name = "binder",  
    //关联Linux文件操作符
    .fops = &binder_fops
};
```
这样，在Binder驱动设备注册完成后，在用户空间调用`poll()`、`open()`等函数的时候，Binder驱动的对应函数就会被调用。

## open()函数的真面目
现在，我们知道了，当我们在用户空间调用`open()`函数时，Binder驱动层的`binder_open()`函数会被随之调用。我们看看`binder_open()`函数做了些什么？  

```
//用户空间调用open()实际调用的是这里
//参数为打开设备文件后传递过来的
static int binder_open(struct inode *nodp, struct file *filp)
{
    //binder_proc储存进程信息的结构体
    //注意，这个进程结构体是存在于Binder内核空间中的
    struct binder_proc *proc;
    //将当前进程的信息储存到binder_proc中
    ...
    //锁定同步
    binder_lock(__func__);
    ...
    //将该进程上下文信息proc保存到Binder驱动的进程树中
    //以便查找使用
    hlist_add_head(&proc->proc_node, &binder_procs);
    // 设置进程id
    proc->pid = current->group_leader->pid;
    ...
    // 将进程信息结构体赋值给文件私有数据
    filp->private_data = proc;
    //释放锁
    binder_unlock(__func__);
    ...
    //在/proc/binder/proc下创建名为进程id的文件，便于查看进程的通讯
    snprintf(strbuf, sizeof(strbuf), "%u", proc->pid);
    return 0;
}
```
这个函数主要的作用是为打开了`/dev/binder`设备文件的进程生成一个专属的进程信息体，然后保存到驱动中。这样，该进程就能和Binder驱动互动了。  

可能有的细心的同学会发现，`open()`函数会返回设备表述符，而`binder_open()`函数看起来只会返回0啊？CoorChice在前面说过，`binder_open()`只是和`open()`产生了关联，但实际打开设备文件的操作还是Linux再进行。想必你也可以看到，`binder_open()`函数的参数是在设备文件打开后才可能获取的。所以，这个设备描述符应该是由Linux来分配给进程的。  

下面，接着看看在`open_driver()`中出现的另一个函数`ioctl()`。

## ioctl()函数的真面目
如果你理解了上面的`open()`函数，那么自然就知道，用户空间的`ioctl()`函数会引起Binder驱动层的`binder_ioctl()`函数的调用。我们就看看驱动层的这个函数做了什么？这是一个十分重要的函数啊！
```
static long binder_ioctl(struct file *filp, unsigned int cmd, unsigned long arg)
{
    ...
    // 从file结构体中取出进程信息
    struct binder_proc *proc = filp->private_data;
    struct binder_thread *thread;
    unsigned int size = _IOC_SIZE(cmd);
    //表明arg是一个用户空间地址
    void __user *ubuf = (void __user *)arg;
    ...
    //取出线程信息
    thread = binder_get_thread(proc);
    ...
    switch (cmd) {
        ...
    }
    ...
}
```
同样，用户层调用`ioctl(fd, cmd, arg)`函数，会先由Linux内核根据设备描述符fd获得对应的设备文件体file，然后调用Binder驱动的`binder_ioctl()`函数。  

这个函数比较重要，CoorChice再一步一步的解析一下。  

![image](http://upload-images.jianshu.io/upload_images/1869462-5361550bb601157f.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)   

### 获得进程信息体
首先Binder驱动根据传入的文件体获得其中的进程信息。

```
struct binder_proc *proc = filp->private_data;
```
还记得在`binder_open()`中生成的那个进程信息结构体吗？

### 来自用户空间的参数

```
void __user *ubuf = (void __user *)arg;
```
首先我们需要知道，这个arg是从用户空间传递过来的地址。比如`ioctl(fd, BINDER_VERSION, &vers)`传递过来了一个地址，指向用来储存Binder版本号的空间。  

在Binder驱动层，需要对这个地址进行转换一下，用`__user`给它做上标记，表明它指向的是用户空间的地址。那么，这个arg指向的空间与Binder驱动的内存空间的数据传递就需要通过`copy_from_user()`或者`copy_to_user()`来进行了。

### 不同的cmd对应不同的操作

```
switch (cmd) {
        ...
    }
```
switch中定义了几个命令，分别对应不同的操作，CoorChice不在这全部说了，后面遇到再说。  

我们先看看在`open_driver()`中出现的两个cmd就行了。
- BINDER_VERSION  
这个命令用于获取Binder驱动版本号。

```
//获取Binder驱动的版本号
case BINDER_VERSION: {
    //表示用户空间的binder版本信息
    struct binder_version __user *ver = ubuf;
    ...
    //把版本号赋值给binder_version的protocol_version成员
    if (put_user(BINDER_CURRENT_PROTOCOL_VERSION,
                 &ver->protocol_version)) {
        ...
    }
    break;
}
```
注意，上面不是直接赋值，而是使用`了put_user()`函数。因为这个值是需要写到用户空间去的。

- BINDER_SET_MAX_THREADS
设置进程可用于Binder通讯的最大线程数量。

```
//设置用户进程最大线程数
case BINDER_SET_MAX_THREADS:
    //使用copy_from_user()函数，将用户空间的数据拷贝到内核空间
    //这里就是把线程数拷贝给进程结构体的max_threads
    if (copy_from_user(&proc->max_threads, ubuf, sizeof(proc->max_threads))) {
        ...
    }
    break;

```
注意，上面使用了`copy_from_user()`函数，把用户空间的值，写到了驱动层的进程信息体的成员max_threads。  

好了，上次`open_driver()`这个坑算是补上了。   

![image](http://upload-images.jianshu.io/upload_images/1869462-fef42d5e090eb5e1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  


接下来看看`ProcessState::getStrongProxyForHandle()`函数留下的坑吧。

# 接着getStrongProxyForHandle()说

注意啦，从这里开始是山路十八弯，抓好扶好了啊！  

先来看一张流程图。  

![image](http://upload-images.jianshu.io/upload_images/1869462-767e29853a35eaf1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

不够高清？[点这个链接下载吧！http://ogemdlrap.bkt.clouddn.com/Binder%E8%BF%9B%E9%98%B6%E5%AE%8C%E6%95%B4.png](http://ogemdlrap.bkt.clouddn.com/Binder%E8%BF%9B%E9%98%B6%E5%AE%8C%E6%95%B4.png)。So Sweet！  

图中相同颜色的流程线表示同一个流程，上面标有数字，你需要按照数字顺序来看，因为这真的是一个复杂无比的流程！  

另外，同一种颜色的双向箭头线指向的是同一个变量或者值相同的变量。同理，相同颜色的带字空心箭头指向的也是同一个变量或者相同的值。  

每个函数框上部的框表示在我们这个流程中，传入函数的参数。
## 温习一下getStrongProxyForHandle()中的坑

```
sp<IBinder> ProcessState::getStrongProxyForHandle(int32_t handle)
{
    sp<IBinder> result;
    
    ...
    //尝试获取handle对应的handle_entry对象，没有的话会创建一个
    handle_entry* e = lookupHandleLocked(handle);
    
    if (e != NULL) {
        IBinder* b = e->binder;
        if (b == NULL || !e->refs->attemptIncWeak(this)) {
            // 上面的判断确保了同一个handle不会重复创建新的BpBinder
            if (handle == 0) {
                Parcel data;
                //在handle对应的BpBinder第一次创建时
                //会执行一次虚拟的事务请求，以确保ServiceManager已经注册
                status_t status = IPCThreadState::self()->transact(0, IBinder::PING_TRANSACTION, data, NULL, 0);
                if (status == DEAD_OBJECT)
                    //如果ServiceManager没有注册，直接返回
                    return NULL;
            }
            //创建一个BpBinder
            //handle为0时创建的是ServiceManager对应的BpBinder
            b = new BpBinder(handle);
            e->binder = b;
            if (b) e->refs = b->getWeakRefs();
            result = b;  //待会儿返回b
        }
        ...
    }
    
    return result;
}
```
上次CoorChice在getStrongProxyForHandle()函数中是把下面这段代码省略了的，为了方便大家关注流程。

```
if (handle == 0) {
    Parcel data;
    //在handle对应的BpBinder第一次创建时
    //会执行一次虚拟的事务请求，以确保ServiceManager已经注册
    status_t status = IPCThreadState::self()->transact(0, IBinder::PING_TRANSACTION, data, NULL, 0);
    if (status == DEAD_OBJECT)
    //如果ServiceManager没有注册，直接返回
    return NULL;
}
```
由于我们发起了获取ServiceManager的Binder的请求，所以handle是0的。还记得吗？应用进程在首次获取（或者说创建）ServiceManager的Binder前，会先和ServiceManager进行一次无意义的通讯（可以看到这次通讯的code为PING_TRANSACTION），以确保系统的ServiceManager已经注册。既然是在这第一次见到Binder通讯，那么我们就索性从这开始来探索Binder通讯机制的核心流程吧。  
## IPCThreadState的创建

```
IPCThreadState::self()->transact(0, IBinder::PING_TRANSACTION, data, NULL, 0)
```
这句代码首先会获取IPCThreadState单例。这是我在图中省略了的。

```
IPCThreadState* IPCThreadState::self()
{
    if (gHaveTLS) {
    restart:
        const pthread_key_t k = gTLS;
        //先检查有没有，以确保一个线程只有一个IPCThreadState
        IPCThreadState* st = (IPCThreadState*)pthread_getspecific(k);
        if (st) return st;
        return new IPCThreadState; //没有就new一个IPCThreadState
    }
    ...
}
```
很明显，这段代码确保了进程中每一线程都只会有一个对应IPCThreadState。  

接下来看看IPCThreadState的构造函数。

```
IPCThreadState::IPCThreadState()
  //保存所在进程
: mProcess(ProcessState::self()),
mMyThreadId(androidGetTid()),
mStrictModePolicy(0),
mLastTransactionBinderFlags(0)
{
    pthread_setspecific(gTLS, this);
    clearCaller();
    //用于接收Binder驱动的数据，设置其大小为256
    mIn.setDataCapacity(256);
    //用于向Binder驱动发送数据，同样设置其大小为256
    mOut.setDataCapacity(256);
}
```
CoorChice注释的地方比较重要哦，想要看懂后面的流程，上面3个注释的记住哦！  

好了，我们的IPCThreadState算是创建出来了。事实上IPCThreadState主要就是封装了和Binder通讯的逻辑，当我们需要进行通讯时，就需要通过它来完成。  

![image](http://upload-images.jianshu.io/upload_images/1869462-db0b255445e72814.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  



下面就来看看通讯是怎么开始的。

## 第一步 IPCThreadState::transact()发起通讯

你可以先在图中找到对应的流程线。transact()完整代码的话你可以看图中的，或者在`/frameworks/native/libs/binder/IPCThreadState.cpp`看源码。由于流程复杂，CoorChice就以小片段来说明。

```
status_t IPCThreadState::transact(int32_t handle,
                                  uint32_t code, const Parcel& data,
                                  Parcel* reply, uint32_t flags)
{
    flags |= TF_ACCEPT_FDS;   //添加TF_ACCEPT_FDS
        ...
     if (err == NO_ERROR) {
         ...
         //将需要发送的数据写入mOut中
         err = writeTransactionData(BC_TRANSACTION, flags, handle, code, data, NULL);
     }
    ...
}
```
首先，在传入的flags参数中添加一个`TF_ACCEPT_FDS`标志，表示返回数据中可以包含文件描述符。以下是几个标志位的意义：

```
enum transaction_flags {
    TF_ONE_WAY     = 0x01, /*异步的单向调用，没有返回值*/
    TF_ROOT_OBJECT = 0x04, /*里面的数据是一个组件的根对象*/
    TF_STATUS_CODE = 0x08, /*数据包含的是一个32bit的状态码*/
    TF_ACCEPT_FDS  = 0x10, /*允许返回对象中，包含文件描述符*/
}
```
接着，会调用writeTransactionData()函数，把需要发送的数据准备好。注意这里的命令是`BC_TRANSACTION`哦。如果你随时对照着图查看参数的话，这个流程将会变的容易理解一些。

## 第二步 writeTransactionData()准备发送数据

```
status_t IPCThreadState::writeTransactionData(int32_t cmd, uint32_t binderFlags,
    int32_t handle, uint32_t code, const Parcel& data, status_t* statusBuffer)
{
    //储存通讯事务数据的结构
    binder_transaction_data tr;
    tr.target.ptr = 0;  //binder_node的地址
    tr.target.handle = handle;  //用于查找目标进程Binder的handle，对应binder_ref
    tr.code = code; //表示事务类型
    tr.flags = binderFlags;
    tr.cookie= 0;
    ...
    //Parcel mOut，与之相反的有Parcel mIn
    //写入本次通讯的cmd指令
    mOut.writeInt32(cmd);
    //把本次通讯事务数据写入mOut中
    mOut.write(&tr, sizeof(tr));
    return NO_ERROR;
}
```
如你所见，这个函数主要创建了一个用于储存通讯事务数据的`binder_transaction_data`结构t，并把需要发送的事务数据放到其中，然后再把这个tr写入IPCThreadState的mOut中。这样一来，后面就可以从mOut中取出这个通讯事务数据结构了。它非常重要，你一定要记住它是什么？以及从那来的？ 

此外，还需要把本次通讯的命令也写入mOut中，这样后面才能获取到发送方的命令，然后执行相应的操作。  

## 第三步 waitForResponse()等待响应
第二步完成后，我们再次回到IPCThreadState::transact()函数中。

```
status_t IPCThreadState::transact(int32_t handle,
                                  uint32_t code, const Parcel& data,
                                  Parcel* reply, uint32_t flags)
{
    ...
    flags |= TF_ACCEPT_FDS;   //添加TF_ACCEPT_FDS
    ...
    //等待响应
    if ((flags & TF_ONE_WAY) == 0) { //检查本次通讯是否有TF_ONE_WAY标志，即没有响应
        //reply是否为空
        if (reply) {
            err = waitForResponse(reply);
        } else {
            Parcel fakeReply;
            err = waitForResponse(&fakeReply);
        }
        ...
    }
    ...
    return err;
}
```
一般通讯都需要响应，所以我们就只看有响应的情况了，即flags中不包含`TF_ONE_WAY`标记。调用waitForResponse()函数时，如果没有reply，会创建一个fakeReplay。我们回顾一下：

```
transact(0, IBinder::PING_TRANSACTION, data, NULL, 0)
```
看，我们上面传入的replay是一个NULL，所以这里是会创建一个fakeReplay的。 

紧接着，我们就进入到`IPCThreadState::waitForResponse()`中了。可以看下图中的流程线哦，对应红色编号3的线。

```
status_t IPCThreadState::waitForResponse(Parcel *reply, status_t *acquireResult)
{
    ...
    while (1) {
        //真正和Binder驱动交互的是talkWithDriver()函数
        if ((err=talkWithDriver()) < NO_ERROR) break;
        ...
    }
    ...
}
```
这个方法中，一开始就有些隐蔽的调用了一个十分重要的方法`IPCThreadState::talkWithDriver()`，从名字也能看出来，真正和Binder驱动talk的逻辑是在这个函数中的。这个地方给差评！   

![image](http://upload-images.jianshu.io/upload_images/1869462-3d35e2abde07d908.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  


顺着代码，我们进入talkWithDriver()看看用户空间是如何和Binder驱动talk的。

## 第四步 talkWithDriver()和Binder talk！
注意，需要说明一下，`IPCThreadState::talkWithDriver()`这个函数的参数默认为true！一定要记住，不然后面就看不懂了！

```
status_t IPCThreadState::talkWithDriver(bool doReceive)
{
    ...
    //读写结构体,它是用户空间和内核空间的信使
    binder_write_read bwr;
    ...
    //配置发送信息
    bwr.write_size = outAvail;
    bwr.write_buffer = (uintptr_t)mOut.data();
    ...
    //获取接收信息
    if(doReceive && needRead){
        bwr.read_size = mIn.dataCapacity();
        bwr.read_buffer = (uintptr_t)mIn.data();
    } else {
        bwr.read_size = 0;
        bwr.read_buffer = 0;
    }
    ...
    //设置消耗为0
    bwr.write_consumed = 0;
    bwr.read_consumed = 0;
    status_t err;
    do {
        ...
        //通过ioctl操作与内核进行读写
        if (ioctl(mProcess->mDriverFD, BINDER_WRITE_READ, &bwr) >= 0)
            err = NO_ERROR;
        ...
    } while (err == -EINTR);
    ...
}
```
这个函数中，有一个重要结构被定义，就是`binder_write_read`。它能够储存一些必要的发送和接收的通讯信息，它就像用户空间和内核空间之间的一个信使一样，在两端传递信息。  

在这个函数中，首先会把用户空间要传递/读取信息放到bwr中，然后通过一句关键的代码`ioctl(mProcess->mDriverFD, BINDER_WRITE_READ, &bwr)`与Binder驱动talk。ioctl()函数CoorChice已经在上一篇中说了，它最终会调用到Binder内核的`binder_ioctl()`函数，至于为什么？你可以再看看上一篇文章回顾下。  

![image](http://upload-images.jianshu.io/upload_images/1869462-9f44e7284928dcc1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

注意这里我们给ioctl()函数传递的参数。
- 第一个参数，是从本进程中取出上面篇中打开并保存Binder设备文件描述符，通过它可以获取到之前生成的file文件结构，然后传给`binder_ioctl()`函数。没印象的同学先看看上篇回顾下这里。
- 第二个参数，是命令，它决定了待会到内核空间中要执行那段逻辑。
- 第三个参数，我们把刚刚定义的信使bwr的内存地址传到内核空间去。  


这些参数是理解后面步骤的关键，不要忘了哦！现在，进入到老盆友`binder_ioctl()`函数中，看看收到用户空间的消息后，它干了什么？

## 第五步 在binder_ioctl()中处理消息

```
static long binder_ioctl(struct file *filp, unsigned int cmd, unsigned long arg)
{
    int ret;
    // 从file结构体中取出进程信息
    struct binder_proc *proc = filp->private_data;
    struct binder_thread *thread;
    unsigned int size = _IOC_SIZE(cmd);
    //表明arg是一个用户空间地址
    //__user标记该指针为用户空间指针，在当前空间内无意义
    void __user *ubuf = (void __user *)arg;
    ...
    //锁定同步
    binder_lock(__func__);
    //取出线程信息
    thread = binder_get_thread(proc);
    ...
    switch (cmd) {
        //读写数据
        case BINDER_WRITE_READ:
            ret = binder_ioctl_write_read(filp, cmd, arg, thread); 
            ...
        }
        ...
    }
    ...
    //解锁
    binder_unlock(__func__);
    ...
}
```
这个函数看过[《从getSystemService()开始，开撸Binder通讯机制：http://www.jianshu.com/p/1050ce12bc1e》](http://www.jianshu.com/p/1050ce12bc1e)的同学应该不会陌生。首先会根据文件描述符获得的file结构，获取到调用ioctl()函数的进程的进程信息，从而再获得进程的线程。然后将arg参数地址转换成有用户空间标记的指针。接着，在switch中根据cmd参数判断需要执行什么操作。这些步骤和上篇文章中是一样的。不同的是，我们这次的cmd命令是`BINDER_WRITE_READ`，表示要进行读写操作。可以看到，接下来的读写逻辑是在`binder_ioctl_write_read()`函数中的。  

嗯，接下来，我们即将进入第6步，看看Binder驱动中的这段读写通讯逻辑是怎样的？

## 第六步 binder_ioctl_write_read() talking

```
static int binder_ioctl_write_read(struct file *filp,
                                   unsigned int cmd, unsigned long arg,
                                   struct binder_thread *thread)
{
    int ret = 0;
    //获取发送进程信息
    struct binder_proc *proc = filp->private_data;
    unsigned int size = _IOC_SIZE(cmd);
    //来自用户空间的参数地址
    void __user *ubuf = (void __user *)arg;
    //读写信息结构体
    struct binder_write_read bwr;
    ...
    //拷贝用户空间的通讯信息bwr到内核的bwr
    if (copy_from_user(&bwr, ubuf, sizeof(bwr)))
    ...
    if (bwr.write_size > 0) {
        //写数据
        ret = binder_thread_write(proc, thread,
                                  bwr.write_buffer,
                                  bwr.write_size,
                                  &bwr.write_consumed);
    ...
}
```
咱们先看上面这个片段。  

首先自然是取出用户空间的进程信息，然后转换获得用户空间的参数地址（对应本次通讯中为bwr的地址），这些都跟在`binder_ioctl()`中做的差不多。  

接下来，你可以看到一个`binder_write_read`结构的申明`struct binder_write_read bwr`，紧跟着通过`copy_from_user(&bwr, ubuf, sizeof(bwr))`把用户空间的bwr拷贝到了当前内核空间的bwr。现在，Binder内核空间的bwr就获取到了来自用户空间的通讯信息了。  

获取到来自用户空间的信息后，先调用`binder_thread_write()`函数来处理，我看看是如何进行处理的。

## 第七步binder_thread_write()处理写入
```
static int binder_thread_write(struct binder_proc *proc,
                               struct binder_thread *thread,
                               binder_uintptr_t binder_buffer, size_t size,
                               binder_size_t *consumed)
{
    uint32_t cmd;
    void __user *buffer = (void __user *)(uintptr_t)binder_buffer;
    void __user *ptr = buffer + *consumed;    //起始地址
    void __user *end = buffer + size;         //结束地址
    while (ptr < end && thread->return_error == BR_OK) {
        //从用户空间获取cmd命令
        if (get_user(cmd, (uint32_t __user *)ptr)) -EFAULT;
        ptr += sizeof(uint32_t);
        switch (cmd) {
            case BC_TRANSACTION:
            case BC_REPLY: {
                //用来储存通讯信息的结构体
                struct binder_transaction_data tr;
                //拷贝用户空间的binder_transaction_data
                if (copy_from_user(&tr, ptr, sizeof(tr)))   return -EFAULT;
                ptr += sizeof(tr);
                //处理通讯
                binder_transaction(proc, thread, &tr, cmd == BC_REPLY);
                break;
            }
                ...
        }
        *consumed = ptr - buffer;
    }
    return 0;
}
```
一开始就是对一些变量进行赋值。   

首先，`binder_buffer`是啥？哪来的？快到到传参的地方方看看`bwr.write_buffer`，它是写的buffer。那么它里面装了啥？这就得回到第4步中找了，因为bwr是在那个地方定义和初始化的。`bwr.write_buffer = (uintptr_t)mOut.data()`，嗯，它指向了mOut中的数据。那么问题又来了？mOut中的数据是啥？...   

![image](http://upload-images.jianshu.io/upload_images/1869462-96565dd83942e45a.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  



看，这就是为什么CoorChice一直在强调，前面的一些参数和变量一定要记住！不然到后面就会云里雾里的！不过还好，有了CoorChcie上面那张图，你随时可以快速的找到答案。我们回到第二步writeTransactionData()，就是通讯事务结构定义的那个地方。看到没，mOut中储存的就是一个通讯事务结构。  

现在答案就明了了，buffer指向了用户空间的通讯事务数据。  

另外两个参数，ptr此刻和buffer的值是一样的，因为consumed为0，所以它现在也相当于是用户空间的通讯事务数据tr的指针；而end可以明显的看出，它指向了tr的末尾。  

通过`get_user(cmd, (uint32_t __user *)ptr)`函数，我们可以将用户空间的tr的cmd拷贝到内核空间。`get_user()`和`put_user()`这对函数就是干这个的，拷贝一些简单的变量。回到第2步`writeTransactionData()`中，看看参数。没错，cmd为`BC_TRANSACTION`。所以，进到switch中，对应执行的就是`case BC_TRANSACTION`。  

可以看到`BC_TRANSACTION`事务命令和`BC_REPLAY`响应命令，执行的是相同的逻辑。

```
//用来储存通讯信息的结构体
struct binder_transaction_data tr;
//拷贝用户空间的binder_transaction_data
if (copy_from_user(&tr, ptr, sizeof(tr)))   return -EFAU
ptr += sizeof(tr);
//处理通讯
binder_transaction(proc, thread, &tr, cmd == BC_REPLY);
```
先定义了一个内核空间的通讯事务数据tr，然后把用户空间的通讯事务数据拷贝到内核中tr。此时，ptr指针移动`sizeof(tr)`个单位，现在ptr应该和end的值是一样的了。然后，调用`binder_transaction()`来处理事务。  

## 第八步 binder_transaction()来处理事务
顺着流程线8看过去，WTF！这是一个复杂无比的函数！很多！很长！  

函数一开始定义了一堆变量，我们先不管，用到时再说。先看第一个使用到的参数`replay`。由于上一步中的cmd为`BC_TRANSACTION`，所以很明显，走的是false，所以我们直接看false里的逻辑。

```
static void binder_transaction(struct binder_proc *proc,
                               struct binder_thread *thread,
                               struct binder_transaction_data *tr, int reply){
    ...
    if (reply) {
        ...
    } else {
        if (tr->target.handle) {
            //如果参数事物信息中的进程的句柄不为0，即不是系统ServiceManager进程
            
            //定义binder引用
            struct binder_ref *ref;
            //根据参数binder进程和句柄handle来查找对应的binder
            ref = binder_get_ref(proc, tr->target.handle);
            ...
            //设置目标binder实体为上面找到的参数进程的binder引用的binder实体
            target_node = ref->node;
        } else {
            //如果参数事物信息中的进程的句柄为0，即是系统ServiceManager进程
            
            //设置通讯目标进程的Binder实体为ServiceManager对应的Binder
            target_node = binder_context_mgr_node;
        }
        //设置通讯目标进程为target_node对应的进程
        target_proc = target_node->proc;
        ...
}
```
一开始先判断`tr->target.handle`为不为0。还记得上一篇说的吗？handle为0表示的是ServiceManager，如果不为0表示的其它Service。那么这里为不为0呢？看图！  

我们顺着指向tr的绿线一直找，可以看到。在用户空间通讯事务数据被定义的地方，也就是第2步`IPCThreadState::writeTransactionData()`中，给`tr->target_handle`赋值了，往上看发现，这个值来自`IPCThreadState::transact()`函数的参数handle。那么回到我们一开始调用这个函数的地方`IPCThreadState::self()->transact(0, IBinder::PING_TRANSACTION, data, NULL, 0)`。哦，handle为0。所以这里的target就是ServiceManager。那么直接把`binder_context_mgr_node`（它表示ServiceManager的Binder，在ServiceManager注册的时候被缓存到了Binder内核中）赋值给`target_node`，记住了哦！后面这些都会用到。总之，我们就是需要先获取到一个目标进程。  

接下来，通过`target_node`，也就是ServiceManager进程的Binder（其它情况就是对应进程的Binder），我们可以获取到目标进程信息，然后赋值给`target_proc`。记住了哦！  

继续下一段代码。

```
static void binder_transaction(struct binder_proc *proc,
                               struct binder_thread *thread,
                               struct binder_transaction_data *tr, int reply){
    ...
    //判断目标线程是否为空
    if (target_thread) {
        ...
        //目标线程的todo队列
        target_list = &target_thread->todo;
        target_wait = &target_thread->wait;
        ...
    } else {
        //获得通讯目标进程的任务队列
        target_list = &target_proc->todo;
        //获取通讯目标进程的等待对象
        target_wait = &target_proc->wait;
    }
    ...
}
```
首先看`target_thread`是否为空，由于我们没有走if(replay)的TRUE逻辑，所以这里`target_thread`是为空的。那么，从目标进程信息`target_proc`分别去除todo任务队列和wait对象，赋值给`target_list`和`target_wait`。同样需要记住！  

继续下一段代码。

```
static void binder_transaction(struct binder_proc *proc,
                               struct binder_thread *thread,
                               struct binder_transaction_data *tr, int reply){
                               
   struct binder_transaction *t; //表示一个binder通讯事务
   struct binder_work *tcomplete; //表示一项work
   ...
   struct list_head *target_list;  //通讯目标进程的事务队列
   wait_queue_head_t *target_wait; //通讯目标进程的等待对象
    ...
    //为本次通讯事务t申请空间
    t = kzalloc(sizeof(*t), GFP_KERNEL);
    ...
    tcomplete = kzalloc(sizeof(*tcomplete), GFP_KERNEL);
    ...
    if (!reply && !(tr->flags & TF_ONE_WAY))
        //采用非one way通讯方式，即需要等待服务端返回结果的通讯方式
        
        //设置本次通讯事务t的发送线程为用户空间的线程
        t->from = thread;
    else
        t->from = NULL;
    ...
    //设置本次通讯事务的接收进程为目标进程
    t->to_proc = target_proc;
    //设置本次通讯事务的接收线程为目标线程
    t->to_thread = target_thread;
    //设置本次通讯事务的命令为用户空间传来的命令
    t->code = tr->code;
    //设置本次通讯事务的命令为用户空间传来的flags
    t->flags = tr->flags;
    ...
    //开始配置本次通讯的buffer
    //在目标进程中分配进行本次通讯的buffer的空间
    t->buffer = binder_alloc_buf(target_proc, tr->data_size,
                                 tr->offsets_size, !reply && (t->flags & TF_ONE_WAY));
    
    t->buffer->allow_user_free = 0; //通讯buffer允许释放
    t->buffer->transaction = t;  //把本次通讯存入buffer中
    //设置本次通讯的buffer的目标Binder实体为target_node
    //如前面一样，通过这个buffer可以找到对应的进程
    t->buffer->target_node = target_node;
    ...
    offp = (binder_size_t *)(t->buffer->data + ALIGN(tr->data_size, sizeof(void *)));
    //将用户空间发送来的数据拷贝到本次通讯的buffer的data中
    copy_from_user(t->buffer->data, (const void __user *)(uintptr_t)tr->data.ptr.buffer, tr->data_size);
    ...
    //将用户空间发送来的偏移量offsets拷贝给起始offp
    copy_from_user(offp, (const void __user *)(uintptr_t)tr->data.ptr.offsets, tr->offsets_size);
    ...
    //计算结尾off_end
    off_end = (void *)offp + tr->offsets_size;
    ...
    //判断是否是BC_REPLY
    if (reply) {
        ...
        
        binder_pop_transaction(target_thread, in_reply_to);
    } else if (!(t->flags & TF_ONE_WAY)) {
        //如果没有ONE_WAY标记，即需要等待响应
        t->need_reply = 1;  //1标示这是一个同步事务，需要等待对方回复。0表示这是一个异步事务，不用等对方回复
        //设置本次通讯事务的from_parent为发送方进程的事务
        t->from_parent = thread->transaction_stack;
        //设置发送方进程的事务栈为本次通讯事务
        thread->transaction_stack = t;
    }
    ...
    
    //将本次通讯事务的work类型设置为BINDER_WORK_TRANSACTION
    t->work.type = BINDER_WORK_TRANSACTION;
    //将本次通讯事务的work添加到目标进程的事务列表中
    list_add_tail(&t->work.entry, target_list);
    
    //设置work类型为BINDER_WORK_TRANSACTION_COMPLETE
    tcomplete->type = BINDER_WORK_TRANSACTION_COMPLETE;
    //将BINDER_WORK_TRANSACTION_COMPLETE类型的work添加到发送方的事务列表中
    list_add_tail(&tcomplete->entry, &thread->todo);
    
    if (target_wait)
        //唤醒目标进程，开始执行目标进程的事务栈
        wake_up_interruptible(target_wait);
    return;
}
```
在开始分析之前，大家先吧这段代码开始的几个变量定义记住，不然后面会很迷茫的！  

这段代码很多！很长！CoorChice已经尽量的删去一些没那么重要的和我不知道是干啥的了！！   

![image](http://upload-images.jianshu.io/upload_images/1869462-8344ee98ef2d4880.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)   

其实这么多代码，主要使用在给binder事务t的成员赋值了。我们简单看几个我认为重要的赋值。  

首先为事务t和work tcomplete申请了内存。然后设置事务t的from线程（也就是发送方线程）的值，如果不是`BC_REPLAY`事务，并且通讯标记没有`TF_ONE_WAY`（即本次通讯需要有响应），那么把参数thread赋值给`t->from`。前面说过，我们本次通讯是`BC_TRANSACTION`事务，所以事务t就需要储存发送方的线程信息，以便后面给发送方响应使用。  

参数thread是那来的呢？顺着往回找，在第5步`binder_ioctl()`中，我们从用户空间调用ioctl()函数的进程（即发送方进程）的进程信息中获取到了thread。  

接着设置事务t的目标进程`t->to_proc`和目标进程的线程`t->to_thread`为前面处理好的`target_proc`和`target_thread`（本次通讯，`target_thread`为空哦）。   

然后把通讯事务数据tr中的code和flags赋值给事务t的code和flags。code和flags是什么呢？我们回到用户空间，定义通讯事务数据，即第2步IPCThreadState::writeTransaction()中可以看到，code和flags均是传进来的参数。而的发源地是通讯的起始点`IPCThreadState::self()->transact(0, IBinder::PING_TRANSACTION, data, NULL, 0)`，即code = `IBinder::PING_TRANSACTION`， flags = 0。记住了哦！后面还会用。  

然后开始设置事务t的buffer信息。首先通过`binder_alloc_buf()`函数，在目标进程`target_proc`中为`t->buffer`申请了内存，即`t->buffer`指向了目标进程空间中的一段内存。然后配置一下`t->buffer`的信息，这些信息后面也会用到。记住了哦！  

接着通过`copy_from_user()`函数，把用户空间的需要发送的数据拷贝到`t->buffer`的data中。  

再往下到了if(replay)，本次通讯会走false逻辑。于是，事务t会把发送方的事务栈`transaction_stack`储存在`from_parent`中，而发送方把自己的事务栈设置以成t开始。这些都需要记住，不然再往后你就会越来越迷糊！  

最重要的部分来了！

```
//将本次通讯事务的work类型设置为BINDER_WORK_TRANSACTION
t->work.type = BINDER_WORK_TRANSACTION;
//将本次通讯事务的work添加到目标进程的事务列表中
list_add_tail(&t->work.entry, target_list);
    
//设置work类型为BINDER_WORK_TRANSACTION_COMPLETE
tcomplete->type = BINDER_WORK_TRANSACTION_COMPLETE;
将BINDER_WORK_TRANSACTION_COMPLETE类型的work添加到发送方的事务列表中
list_add_tail(&tcomplete->entry, &thread->todo);

if (target_wait)
    //唤醒目标进程，开始执行目标进程的事务栈
    wake_up_interruptible(target_wait);
return;
```
先把事务t的work.type类型设置为`BINDER_WORK_TRANSACTION`类型，这决定了该事务后面走的流程，然后把事务t的任务添加到目标进程的任务栈`target_list`中。接着把work tcomplete的类型设置为`BINDER_WORK_TRANSACTION_COMPLETE`，用于告诉发送方，和Binder驱动的一次talk完成了，同样，需要把这个项任务添加到发送方的任务列表里。  

最后，通过`wake_up_interruptible(target_wait)`函数唤醒休眠中的目标进程，让它开始处理任务栈中的任务，也就是刚刚我们添加到`target_list`中的任务。接着return结束该函数。   

结束这个函数你以为就忘啦？Native！接着往下看。

## 第9步 binder_thread_read()读取数据
上一个函数结束后回到第7步`binder_thread_write()`函数中，`retrun 0;`，`binder_thread_write()`函数结束。然后回到第6步`binder_ioctl_write_read()`函数中继续执行。

```
static int binder_ioctl_write_read(struct file *filp,
                                   unsigned int cmd, unsigned long arg,
                                   struct binder_thread *thread)
{
    ...
    if (bwr.read_size > 0) {
        //读数据
        ret = binder_thread_read(proc, thread, bwr.read_buffer,
                                 bwr.read_size,
                                 &bwr.read_consumed,
                                 filp->f_flags & O_NONBLOCK);
        ...
}
```
Binder驱动会调用`binder_thread_read()`函数，为发送进程读取数据。我们看看是怎么读取的。

```
static int binder_thread_read(struct binder_proc *proc,
                              struct binder_thread *thread,
                              binder_uintptr_t binder_buffer,
                              size_t size,
                              binder_size_t *consumed,
                              int non_block)
{
    ...
    while (1) {
        uint32_t cmd;
        struct binder_transaction_data tr;
        struct binder_work *w;
        struct binder_transaction *t = NULL;
        if (!list_empty(&thread->todo)) {
            //获取线程的work队列
            w = list_first_entry(&thread->todo, struct binder_work, entry);
        } else if (!list_empty(&proc->todo) && wait_for_proc_work) {
            //获取从进程获取work队列
            w = list_first_entry(&proc->todo, struct binder_work, entry);
        } else {
            //没有数据,则返回retry
            if (ptr - buffer == 4 &&
                !(thread->looper & BINDER_LOOPER_STATE_NEED_RETURN))
                goto retry;
            break;
        }
    ...
}

```
我们先看这个片段，前面一堆代码掠过了。首先，需要看看能不能从发送进程的线程thread的任务栈中取出任务来，回顾第8步`binder_transaction()`中，我们在最后往发送进程的线程thread的任务栈中添加了一个`BINDER_WORK_TRANSACTION_COMPLETE`类型的work。所以这里是能取到任务的，就直接执行下一步了。

```
static int binder_thread_read(struct binder_proc *proc,
                              struct binder_thread *thread,
                              binder_uintptr_t binder_buffer,
                              size_t size,
                              binder_size_t *consumed,
                              int non_block)
{
    void __user *buffer = (void __user *)(uintptr_t)binder_buffer;
    void __user *ptr = buffer + *consumed; //
    void __user *end = buffer + size; //用户空间结束
    ...
    while (1) {
        uint32_t cmd;
        struct binder_transaction_data tr;
        struct binder_work *w;
        ...
        switch (w->type) {
            ...
            case BINDER_WORK_TRANSACTION_COMPLETE:
                //设置cmd为BR_TRANSACTION_COMPLETE
                cmd = BR_TRANSACTION_COMPLETE;
                //将BR_TRANSACTION_COMPLETE写入用户进程空间的mIn中
                put_user(cmd, (uint32_t __user *)ptr)；
                //从事务队列中删除本次work
                list_del(&w->entry);
                //释放
                kfree(w);
                break;
            ...
        }
    }
    ...
}
```
由于这个流程中，我们知道work的type为`BINDER_WORK_TRANSACTION_COMPLETE`类型，所以就先只看这种情况了。在这段代码中，`cmd = BR_TRANSACTION_COMPLETE`很重要，要记住！接着把cmd拷贝到用户空间的发送进程，然后删除任务，释放内存。

## 一次和Binder驱动的通讯完成！
上面代码执行完后，`binder_thread_read()`函数差不多就结束了，接着又会回到`binder_ioctl_write_read()`函数。

```
static int binder_ioctl_write_read(struct file *filp,
                                   unsigned int cmd, unsigned long arg,
                                   struct binder_thread *thread)
{
    ...
    //将内核的信使bwr拷贝到用户空间
    if (copy_to_user(ubuf, &bwr, sizeof(bwr)))
    ...
}
```
上面函数最后会把内核中的信使拷贝到用户空间。  

然后，我们直接的再次的回到第3步的函数`IPCThreadState::waitForResponse()`中。
```
status_t IPCThreadState::waitForResponse(Parcel *reply, status_t *acquireResult)
{
    ...
    while (1) {
        //真正和Binder驱动交互的是talkWithDriver()函数
        if ((err=talkWithDriver()) < NO_ERROR) break;
        err = mIn.errorCheck();
        ...
        if (mIn.dataAvail() == 0) continue;
        //取出在内核中写进去的cmd命令
        cmd = mIn.readInt32();
        ...
        
        switch (cmd) {
            //表示和内核的一次通讯完成
            case BR_TRANSACTION_COMPLETE:
                if (!reply && !acquireResult) goto finish;
                break;
            ...
        }
        
    }
    ...
}
```
经过刚刚的读取，这次mIn中可是有数据了哦！我们从mIn中取出cmd命令。这是什么命令呢？就是刚刚写到用户空间的`BR_TRANSACTION_COMPLETE`。在这段逻辑中，由于之前我们传入了一个fakeReplay进来，所以程序走bredk，然后继续循环，执行下一次`talkWithDriver()`函数。到此，我们和Binder内核的一次通讯算是完成了。  

但是我们发起的这次通讯还没有得到回应哦！猜猜看回应的流程是怎样的呀？  

![image](http://upload-images.jianshu.io/upload_images/1869462-aaf2025a89bbb99b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)   

> 文章太长了，回应流程放到下一篇了。

# 总结

> - 抽出空余时间写文章分享需要动力，还请各位看官动动小手点个赞，给我点鼓励😄
> - 我一直在不定期的创作新的干货，想要上车只需进到我的【个人主页】点个关注就好了哦。发车喽～  


本篇CoorChice填了上篇文章中的一些坑，并借此跑通了一遍客户端和Binder驱动通讯的流程。这是个很复杂的过程，大家看着图走一遍，再思考思考。回过头来一想，其实也没那么难了。  

俗话说`会者不难, 难者不会`，大概就是这样吧。




功力有限，有错还请指出一起交流交流。


**看到这里的童鞋快奖励自己一口辣条吧！**    


>  #### 想要看CoorChice的更多文章，请点个关注哦！
