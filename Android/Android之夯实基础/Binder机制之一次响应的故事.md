![image](http://upload-images.jianshu.io/upload_images/1869462-fc6519c71275b5eb.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
  
  

> Binder系列第一篇:[《从getSystemService()开始，开撸Binder通讯机制》http://www.jianshu.com/p/1050ce12bc1e](http://www.jianshu.com/p/1050ce12bc1e)  

> Binder系列第二篇:[《能用【白话文】来分析Binder通讯机制？》http://www.jianshu.com/p/fe816777f2cf](http://www.jianshu.com/p/fe816777f2cf)  

> Binder系列第三篇:[《Binder机制之一次响应的故事》http://www.jianshu.com/p/4fba927dce05](http://www.jianshu.com/p/4fba927dce05)   


 

  
  在上一篇文章[《能用【白话文】来分析Binder通讯机制？》http://www.jianshu.com/p/fe816777f2cf](http://www.jianshu.com/p/fe816777f2cf) 中，我们已经跑通了一遍客户端和Binder内核的一次通讯。即客户端请求一次通讯，到Binder内核给予客户端一个回应。没有印象的通讯可能需要回头再回顾一下了。本篇CoorChice将接着上一次由于篇幅太长而无法继续讲的回应流程。  

事实上，对于我们一开始发起的通讯请求`status_t status = IPCThreadState::self()->transact(0, IBinder::PING_TRANSACTION, data, NULL, 0)` 而言，我们只进行了这次请求处理流程的一半。还记得这个请求是那发送的和干什么的吗？在上一篇流程起始的地方再回顾下哦。   

![image](http://upload-images.jianshu.io/upload_images/1869462-1d1d004e0b88e16c.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  


好了，接着上次来！  

首先，在此放出Binder通讯机制的完整流程图！整个流程很长，而且有些绕人，同学们一定要经常回过来看图啊。  

![image](http://upload-images.jianshu.io/upload_images/1869462-8eb0d9aa5b4edd7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

好吧，再放个高清的下载地址：[http://ogemdlrap.bkt.clouddn.com/Binder%E8%BF%9B%E9%98%B6%E5%AE%8C%E6%95%B4.png](http://ogemdlrap.bkt.clouddn.com/Binder%E8%BF%9B%E9%98%B6%E5%AE%8C%E6%95%B4.png)

# 通讯发起端的休眠ING
上一次CoorChice说到，发起端进程在收到Binder内核响应的`BR_TRANSACTION_COMPLETE`后，再一次的进入了`IPCThreadState::talkWithDriver()`函数。这就意味着发起端需要和Binder内核再通讯一次。  

而这一次通讯，由于没有需要发送的的数据，所以我们直接看Binder内核中的`binder_thread_read()`函数，在图中找到对应的位置进入状态了啊。至于是怎么到这个函数的，你可能需要回顾下上一篇中的内容了，它们基本是一样的，只是跳过了`binder_thread_write()`而已。  

那么我们看看这一次发起端进程在`binder_thread_read()`函数中被进行了怎样的处理。

```
static int binder_thread_read(struct binder_proc *proc,
                              struct binder_thread *thread,
                              binder_uintptr_t binder_buffer,
                              size_t size,
                              binder_size_t *consumed,
                              int non_block)
{
    ...
    //检查是否需要等待进程work完成
    wait_for_proc_work = thread->transaction_stack == NULL && list_empty(&thread->todo);
    ...
    if (wait_for_proc_work) {
        ...
    } else {
        if (non_block) {
            ...
        } else
            //如果不需要等待，进程进入休眠，等待唤醒
            ret = wait_event_freezable(thread->wait, binder_has_thread_work(thread));
    }
    ...
```
我们只看关键的。还记得上一篇在第8步`binder_transaction()`函数中，我们最后把添加到目标进程中的事务t同时赋值给了发送端进程的`thread->transaction_stack`。所以此处的`thread->transaction_stack`是不为NULL的。从而`wait_for_proc_work`就为false，于是就执行到了`ret = wait_event_freezable(thread->wait, binder_has_thread_work(thread));`这一句，然后发送端进程就进入了休眠。目的是为了等待目标进程处理完请求后给予它响应。

# 来自目标进程的回应
在此之前，我们一直关注的都是发送端进程的流程。现在，发送端进程进入了休眠，是时候来看一看本次通讯的目标进程了。在上一篇的第8步`binder_transaction()`函数中，我们把发送端的诉求封装进了一个事务t中，然后把事务t添加到了目标进程的事务队列中，最后把目标进程唤醒了。  

接下来我们看看目标进程被唤醒之后都干了些什么？

## 第一步 从沉睡中醒来
这次我们关注图中的紫色流程线，从编号为1的线开始看。  

```
static int binder_thread_read(struct binder_proc *proc,
                              struct binder_thread *thread,
                              binder_uintptr_t binder_buffer,
                              size_t size,
                              binder_size_t *consumed,
                              int non_block)
{
    ...
    ret = wait_event_freezable_exclusive(proc->wait, binder_has_proc_work(proc, thread));
    ...
    if (ret)
        return ret;
    ...
}
```
由于是通过正常的`wake_up_interruptible()`函数进行唤醒，所以返回的ret = 0。接着，目标进程会进入switch，根据之前设置的cmd执行相应逻辑。
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
        
        switch (w->type) {
            case BINDER_WORK_TRANSACTION:
                //根据队列中的work获取包含该work的事务
                t = container_of(w, struct binder_transaction, work);
                break;
                
            ...
        }
    ...
}
```
这部分需要时刻结合上一篇中的第8步`binder_transaction()`函数来看，很多参数都是来自于那的。  

根据之前的逻辑，我们并没有给目标进程的`thread->todo`赋值，而是给它的`proc->todo`赋了值。因此，我们从`proc->todo`的任务队列中取出之前添加的任务。因为任务类型是`BINDER_WORK_TRANSACTION`，所以swicth中走了`BINDER_WORK_TRANSACTION`的逻辑，获取到通讯事务t。  

接着看下面的逻辑。
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
        ...
        //只有BINDER_WORK_TRANSACTION命令，即t不为空才能继续往下执行
        if (!t)
            continue;
        
        //判断事物t中是否有目标进程的Binder实体
        if (t->buffer->target_node) {
            struct binder_node *target_node = t->buffer->target_node;
            tr.target.ptr = target_node->ptr;
            tr.cookie =  target_node->cookie;
            t->saved_priority = task_nice(current);
            ...
            cmd = BR_TRANSACTION;  //设置命令为BR_TRANSACTION
        } else {
            tr.target.ptr = NULL;
            tr.cookie = NULL;
            cmd = BR_REPLY; //设置命令为BR_REPLY
        }
        //给刚刚定义的事务信息体设置成员的值
        tr.code = t->code;
        tr.flags = t->flags;
        tr.sender_euid = t->sender_euid;
        ...

    ...
}
```
对于if(!t)，很明显，我们是有事务t，所以代码会接着往下执行。  

我们之前有`t->buffer->target_node = target_node;`，所以这里`t->buffer->target_node)`是不为空的。然后，取出这个`target_node`，也就是目标进程的Binder实体。  

接着，把目标进程的Binder实体的引用和cookie缓存保存到新定义的通讯数据tr中。然后，设置命令为`BR_TRANSACTION`，这些都需要记好了哦。  

![image](http://upload-images.jianshu.io/upload_images/1869462-c9cbc9cfeb41ea8b.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
static int binder_thread_read(struct binder_proc *proc,
                              struct binder_thread *thread,
                              binder_uintptr_t binder_buffer,
                              size_t size,
                              binder_size_t *consumed,
                              int non_block)
{
    ...
        tr.data_size = t->buffer->data_size;
        tr.offsets_size = t->buffer->offsets_size;
        tr.data.ptr.buffer = (void *)t->buffer->data +
        proc->user_buffer_offset;
        tr.data.ptr.offsets = tr.data.ptr.buffer +
        ALIGN(t->buffer->data_size,
              sizeof(void *));
        
        //将cmd和数据写回用户空间的mIn
        if (put_user(cmd, (uint32_t __user *)ptr))
            return -EFAULT;
        ptr += sizeof(uint32_t);
        //刚刚赋值好的事务信息写会用户空间的mIn
        if (copy_to_user(ptr, &tr, sizeof(tr)))
            return -EFAULT;
        ptr += sizeof(tr);
        //删除本次work
        list_del(&t->work.entry);
        ...
        kfree(t); //通信完成,则运行释放
        break;
    }
    ...
    return 0;

    ...
}
```
接下来就是把事务t中的数据储存到新定义的通讯数据tr中。然后把刚刚的cmd拷贝到目标进程空间，再把新定义的通讯数据tr也拷贝到目标进程空间。最后删除本次事务，释放内存，结束该函数。

## 第三步 退出ioctl()函数
上一步函数结束后，函数基本都是一路退出，直到Binder内核的`binder_ioctl()`函数。至此，由于我们的目标进程是ServiceManager，所以`binder_ioctl()`函数执行完后，返回的是`/frameworks/native/cmds/servicemanager/binder.c`中的`binder_looper()`。所以，从这里开始，我们转为关注黄色的流程线。  

至于为什么会回到`binder_looper()`函数，这与ServiceManager的机制有关。由于它在系统中既是一个Service，又是一个守护进程，它需要不断的循环等待Client的请求。处理完一个请求，就再次进入循环等待下一个请求。而这个函数就是为它提供这种能力的。这里我们先不讨论ServiceManager工作原理，继续把目光聚焦在我们本次通讯的流程上。  

我们进入`/frameworks/native/cmds/servicemanager/binder.c`中的`binder_looper()`看看在这个流程中它有什么用？

```
void binder_loop(struct binder_state *bs, binder_handler func)
{
    ...
    
    for (;;) {
        bwr.read_size = sizeof(readbuf);
        bwr.read_consumed = 0;
        bwr.read_buffer = (uintptr_t) readbuf;
        ...
        //和binder通讯，等待Client链接
        res = ioctl(bs->fd, BINDER_WRITE_READ, &bwr);
        ...
        //解析Client的数据
        res = binder_parse(bs, 0, (uintptr_t) readbuf, bwr.read_consumed, func);
        ...
    }
}
```
可以看到，ServiceManager通过一个无限循环，反复的进行`ioctl()`和`binder_parse()`操作。还记得第一步中，在没有消息时，ServiceManager会在Binder内核的`binder_thread_read()`函数中被休眠。  

这里，从`ioctl()`函数出来后，表明ServiceManager收到了Binder内核传递过来的信息，所以下一步就是要多信息进行处理。  

## 第四步 binder_parse()处理信息

```
int binder_parse(struct binder_state *bs, struct binder_io *bio,
                 uintptr_t ptr, size_t size, binder_handler func)
{
    int r = 1;
    uintptr_t end = ptr + (uintptr_t) size;
    
    while (ptr < end) {
        uint32_t cmd = *(uint32_t *) ptr;
        ptr += sizeof(uint32_t);
        ...
}
```
首先关注参数ptr，它是ServiceManager传到内核空间的信使`binder_write_read` bwr的读取缓冲区`read_buffer`。在第二步`binder_thread_read()`中，我们在这个buffer中装了一个命令`BR_TRANSACTION`和一个事物数据tr。忘了快回过头看看吧。  

end变量可以指向这个缓冲区结束位置，而此时的ptr是指向缓冲区的头部。所以能够进入while循环。接着从缓冲区中取出刚刚存入的命令cmd，然后将ptr移动一个cmd的距离。


```
int binder_parse(struct binder_state *bs, struct binder_io *bio,
                 uintptr_t ptr, size_t size, binder_handler func)
{
    ...
    
    while (ptr < end) {
        ...
        switch(cmd) {
            case BR_NOOP:
                break;
                //通知ServiceManager通讯事务完成
            case BR_TRANSACTION_COMPLETE:
                break;
                ...
                //执行事务
            case BR_TRANSACTION: {
                //获取通讯事务数据
                struct binder_transaction_data *txn = (struct binder_transaction_data *) ptr;
                ...
                if (func) {
                    unsigned rdata[256/4];
                    // 用于保存"Binder驱动反馈的信息"
                    struct binder_io msg;
                    // 用来保存"回复给Binder驱动的信息"
                    struct binder_io reply;
                    int res;
                    // 初始化reply
                    bio_init(&reply, rdata, sizeof(rdata), 4);
                    // 根据txt(Binder驱动反馈的信息)初始化msg
                    bio_init_from_txn(&msg, txn);
                    // 消息处理
                    res = func(bs, txn, &msg, &reply);
                    // 反馈消息给Binder驱动
                    binder_send_reply(bs, &reply, txn->data.ptr.buffer, res);
                }
                ptr += sizeof(*txn);
                break;
            }
            case BR_REPLY: {
                ...
                break;
            }
                ...
        }
    }
    
    return r;
}
```
前面说过Binder驱动传过来的cmd是`BR_TRANSACTION`类型，所以我们只看相关逻辑。  

在`BR_TRANSACTION`中，首先要做的就是获得从Binder内核读取到的信息。可以看到，将参数ptr转换成了`binder_transaction_data`指针。接下来初始化两个`binder_io`结构，msg用来储存从Binder内核读取的信息，replay用来储存待会儿需要反馈给请求端的信息。然后通过`bio_init_from_txn(&msg, txn)`函数将从Binder内核读取的信息，存入msg中。  

然后我们看到，这里调用了一个fun()函数，它是一个函数指针，代表着`svcmgr_handler()函数`。不难看出，它就是ServiceManager用于处理Binder内核消息的函数。  

## 第五步 svcmgr_handler()处理请求消息
```
int svcmgr_handler(struct binder_state *bs,
                   struct binder_transaction_data *txn,
                   struct binder_io *msg,
                   struct binder_io *reply)
{
    struct svcinfo *si;
    uint16_t *s;
    size_t len;
    uint32_t handle;
    uint32_t strict_policy;
    int allow_isolated;
    ...
    
    //测试指令PING_TRANSACTION直接返回
    if (txn->code == PING_TRANSACTION)
        return 0;
    
    ...
}
```
首先我们知道，`binder_transaction_data`装有来自Binder内核的消息。它装有的消息基本就是请求发起进程发送的消息，也就是其发送的`binder_transaction_data`里的信息。这部分你需要看上一篇的第八步`binder_transaction()`和这一篇中的第一步`binder_thread_read()`，它们中关于`binder_transaction_data`结构的赋值逻辑需要理清楚。   


既然如此，那么我们就回过头看发起请求的进程在`binder_transaction_data`结构中装了什么信息。我们需要回到`IPCThreadState::writeTransactionData()`函数中，也就是初始化发送信使`binder_transaction_data`的地方。在这里，我们可以看到，信使中的code码是参数传进来的。我们沿着这条线往回找，可以发现，就是一开始我们发起这次Binder通讯的地方`status_t status = IPCThreadState::self()->transact(0, IBinder::PING_TRANSACTION, data, NULL, 0)` ，设置了code为`IBinder::PING_TRANSACTION`。真想大白，code为`PING_TRANSACTION`类型就什么也不干，直接退出这个函数。这就是为什么说我们发起这次请求只是测试一下ServiceManager是否已经注册的原因。  

实际上，如果code为其它情况的话，在这里会进行一些其它的逻辑。比如，添加一个Service或者查找获得一个Service。在后面遇到了再回过头了说。   

![image](http://upload-images.jianshu.io/upload_images/1869462-59419b9694bd34bc.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 第六步 binder_send_reply()发送处理结果
上一步，`service_manager.c`中的`svcmgr_handler()`函数中，对请求端的请求进行了处理。然后函数退出，回到`binder.c`的`binder_parse()`继续执行。下一步就是给予请求端处理结果，即这一次ServiceManager要给请求端发送消息了。  


```
void binder_send_reply(struct binder_state *bs,
                       struct binder_io *reply,
                       binder_uintptr_t buffer_to_free,
                       int status)
{
    struct {
        uint32_t cmd_free;
        binder_uintptr_t buffer;
        uint32_t cmd_reply;
        struct binder_transaction_data txn;
    } __attribute__((packed)) data;
    //把回复数据放到data中
    data.cmd_free = BC_FREE_BUFFER;
    data.buffer = buffer_to_free;
    data.cmd_reply = BC_REPLY;
    data.txn.target.ptr = 0;
    data.txn.cookie= 0;
    data.txn.code = 0;
    if (status) {
       ...
    } else {
        data.txn.flags = 0;
        data.txn.data_size = reply->data - reply->data0;
        data.txn.offsets_size = ((char*) reply->offs) - ((char*) reply->offs0);
        data.txn.data.ptr.buffer = (uintptr_t)reply->data0;
        data.txn.data.ptr.offsets = (uintptr_t)reply->offs0;
    }
    //发送响应
    binder_write(bs, &data, sizeof(data));
}
```
上面这段代码不长，它主要干了两个事：
- 定义并初始化储存回复信息的data结构体。其中，`cmd_replay`是本次通讯的指令，后面进行什么逻辑，是更具这个值判断的。
- 调用`binder_write()`函数，准备和Binder内核通讯，从而将回复信息传递给发送端。

## 第七步 binder_write()发送回复信息

```
int binder_write(struct binder_state *bs, void *data, size_t len)
{
    struct binder_write_read bwr;
    int res;
    //初始化信使
    bwr.write_size = len;
    bwr.write_consumed = 0;
    bwr.write_buffer = (uintptr_t) data;
    bwr.read_size = 0;
    bwr.read_consumed = 0;
    bwr.read_buffer = 0;
    //通过Binder发送响应
    res = ioctl(bs->fd, BINDER_WRITE_READ, &bwr);
    ...
    return res;
}
```
啊哈，上面的`binder_write_read`结构体是不是很熟悉的样子？还记得IPCThreadState::talkWithDriver()中也构造了一个吗？它负责将需要发送的信息带到Binder内核，从而传递给通讯目标。  

和Binder内核的通讯同样是通过ioctl()函数进行的。这会让代码逻辑回到`/drivers/staging/android/binder.c`中的`binder_ioctl()`函数中。  

如果上一篇的流程你已经理清楚的话，后面的逻辑你因该都能猜到了。  

![image](http://upload-images.jianshu.io/upload_images/1869462-b6b24bf0f7360b40.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 第八步 在Binder内核中读写
我们回到`/drivers/staging/android/binder.c`中的`binder_ioctl()`函数中，在图中找到对应位置哦！  

逻辑其实和上一篇读取发送端消息，然后在传输给接收端大致是差不多的。只是中间的一些cmd不同，某些步骤执行的操作有区别罢了。CoorChice在上一篇中也有提到。  

在`binder_ioctl()`函数中，同样先获取到发送过来的消息参数，然后由于调用ioctl()的参数是`BINDER_WRITE_READ`，所以直接进入`binder_ioctl_write_read()`函数。大家在图中找到对应位置。在这个函数中，由于是发送消息，所以会进入`binder_thread_write()`函数。  

`binder_thread_write()`函数还是和之前差不多，先读取发送端的消息信息等，然后根据信使`binder_write_read`的`write_buffer`中的cmd决定执行什么逻辑。而这个cmd的值你可以回到第六步`binder_send_reply()`中看看，它是`BC_REPLY`类型的。那么就简单了，和上一篇发送端逻辑一样，会进入到`binder_transaction()`函数中。  


`binder_transaction()`函数就是之前说的有些复杂的函数，大家在图中继续找到对应位置啊。通过上一篇的阅读，相信你已经知道了这个函数主要会根据发送端信息（此时为ServiceManager）和接收端（原本的发送端）的信息，构造两个事务分别添加到发送端和接收端进程的事务列表中。同样，一个事务是给发送端通知本次你发送的信息Binder内核已经转发给接收端了，另一个事务自然是将发送端的消息带到接收端中处理。  


`binder_transaction()`函数执行完后，对ServiceManager而言，由于执行的事务是告知ServiceManager本次Transication完成了，消息已经发送出去，即`BINDER_WORK_TRANSACTION_COMPLETE`命令，所以逻辑同上一篇的基本一致，CoorChice就不重复说了。总之函数一路执行完毕，然后回到`/frameworks/native/cmds/servicemanager/binder.c`的`binder_parse()`中。你可以在这个函数中看到：

```
...
 case BR_TRANSACTION_COMPLETE:
                break;
...
```
在图中找找对应位置哦。收到`BR_TRANSACTION_COMPLETE`指令后，直接break。然后`binder_parse()`函数执行完毕。代码逻辑回到第三步的`binder_loop()`中，由于有一个for循环，所以会再次执行到

```
void binder_loop(struct binder_state *bs, binder_handler func)
{
    ...
    
    for (;;) {
       ...
        //和binder通讯，等待Client链接
        res = ioctl(bs->fd, BINDER_WRITE_READ, &bwr);
        ...
    }
}
```
这一次和Binder内核ioctl通讯，由于没数据要发，所以会一路进入`/drivers/staging/android/binder.c`的`binder_thread_read()`函数中。就在第一步醒来的地方再次进入休眠，等待下一次的唤醒。

```
//休眠，等待下一次唤醒
ret = wait_event_freezable_exclusive(proc->wait, binder_has_proc_work(proc, thread));
```
至此，以我们本次通讯为例，ServiceManager的任务算是完成了。  

# 通讯发起端收到来自接收端的响应

> #### 约定: 本次通讯中，通讯发起端称为C，ServiceManager称为S。  

下面筒靴们跟着紫色流程线看。  

![image](http://upload-images.jianshu.io/upload_images/1869462-2f8809ad8f23b3de.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 第一步 通讯发起端醒来
开篇我们说过，通讯发起端在`/drivers/staging/android/binder.c`的`binder_thread_read()`函数中的``ret = wait_event_freezable(thread->wait, binder_has_thread_work(thread));`未知休眠了，那么这一次被唤醒的自然是我们通讯发起端。 

## 第二步 读取S端的响应信息

同上面的逻辑一样，C在醒来后会从事务中读取来自S的响应信息。在`binder_thread_read()`函数中找到下面这段代码。

```
...
//判断事物t中是否有目标进程的Binder实体
        if (t->buffer->target_node) {
            ...
        } else {
            tr.target.ptr = NULL;
            tr.cookie = NULL;
            cmd = BR_REPLY; //设置命令为BR_REPLY
        }
...
//将cmd和数据写回用户空间的mIn
        if (put_user(cmd, (uint32_t __user *)ptr))
            return -EFAULT;
        ptr += sizeof(uint32_t);
        //刚刚赋值好的事务信息写会用户空间的mIn
        if (copy_to_user(ptr, &tr, sizeof(tr)))
            return -EFAULT;
...
```
由于在`binder_transaction()`函数中，由于S的cmd为`BC_REPLAY`，所以走的是reply的逻辑。这意味着`t->buffer->target_node`为NULL。所以这一步，会走上面的逻辑。注意，此时cmd被设置成了`BR_REPLY`。

## 第三步 C读取S的响应
上一步执行完毕后，C就一路退出，结束和Binder内核的通讯。即会从`/frameworks/native/libs/binder/IPCThreadState.cpp`的`IPCThreadState::talkWithDriver()`中退出，然后回到`IPCThreadState::waitForResponse()`中。  

上一步中，我们知道cmd被赋值为`BR_REPLY`，然后写到了C中。所以看看`IPCThreadState::waitForResponse()`中的switch逻辑吧，知道会往那执行了不？

```
status_t IPCThreadState::waitForResponse(Parcel *reply, status_t *acquireResult)
{
    ...
    while (1) {
        //真正和Binder驱动交互的是talkWithDriver()函数
        if ((err=talkWithDriver()) < NO_ERROR) break;
        ...
        //取出在内核中写进去的cmd命令
        cmd = mIn.readInt32();
        ...
        
        switch (cmd) {
            ...
            case BR_REPLY:
            {
                binder_transaction_data tr;
                //读取内核中写入的事务数据
                err = mIn.read(&tr, sizeof(tr));
                ...
                if (reply) {
                    //通常这里的reply都不会为空
                    if ((tr.flags & TF_STATUS_CODE) == 0) {
                        reply->ipcSetDataReference(
                           reinterpret_cast<const uint8_t*>(tr.data.ptr.buffer),
                           tr.data_size,
                           reinterpret_cast<const binder_size_t*>(tr.data.ptr.offsets),
                           tr.offsets_size/sizeof(binder_size_t),
                           freeBuffer, this);
                    } else {
                        err = *reinterpret_cast<const status_t*>(tr.data.ptr.buffer);
                        ...
                    }
                }
                ...
            }
        }
    }
    ...
    return err;
}
```
没错，走的是获取响应信息的`BR_REPLY`逻辑。首先会把内核中传递过来的事务tr读取出来，由于前面tr是有值的，所以这里`err = NO_ERROR`。接着，reply在上一篇中说过，它是初始化过的，所以不为NULL，进入reply逻辑。tr.flags的值是来自与S的，具体在上面第六步的`binder_send_reply()`函数中，有这样一句`data.txn.flags = 0`。忘记了翻回去找找哦。因此，这里调用了reply的`Parcel::ipcSetDataReference()`函数，写入响应信息。然后该函数就该返回了。注意此时err仍然为`NO_ERROR`。


## 第四步 退出waitForResponse()函数
上一步waitForResponse()函数返回了一个`NO_ERROR`，然后回到`IPCThreadState::transact()`函数中。接着，这个函数也没什么逻辑需要执行了，就将err返回。

## 第五步 一次完整的通讯终于完成啦！
`IPCThreadState::transact()`函数结束后，我们终于回到了最初的地方，那个通讯发起的地方

```
sp<IBinder> ProcessState::getStrongProxyForHandle(int32_t handle)
{
    sp<IBinder> result;
    
    ...
    //在handle对应的BpBinder第一次创建时
    //会执行一次虚拟的事务请求，以确保ServiceManager已经注册
    status_t status = IPCThreadState::self()->transact(0, IBinder::PING_TRANSACTION, data, NULL, 0);
    if (status == DEAD_OBJECT)
        //如果ServiceManager没有注册，直接返回
        return NULL;
    ...
}
```
此时，status为`NO_ERROR`，即ServiceManager已经注册了。


# 总结

> - 抽出空余时间写文章分享需要动力，还请各位看官动动小手点个赞，给我点鼓励😄
> - 我一直在不定期的创作新的干货，想要上车只需进到我的【个人主页】点个关注就好了哦。发车喽～  


这一篇，我们终于通关了一遍Binder通讯机制的完整流程。确实很长！很长！很长！  

![image](http://upload-images.jianshu.io/upload_images/1869462-cca4a452886a9839.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  


其实大家可以看到，阅读源码时画一画流程图是很有必要的，特别是对这种复杂的源码。边看边画比较容易梳理清楚，不然流程太长会看着后面忘了前面的！有了图就可以随时回顾到忘记的地方。我们画图不一定要套用标准的流程图、过程图之类的，选择适合自己的方式就好。  

通过这3篇文章，我们从一次`getSystemService()`的调用开始，一步步深入到了Binder机制的核心中，最终明白了Binder机制到底是如何运作的，为什么它能够撑起整个Android系统。一个简单的接口调用，背后往往蕴藏着一套精心设计的逻辑，这就是编程的魅力所在吧。


**看到这里的童鞋快奖励自己一口辣条吧！**    


>  #### 想要看CoorChice更多的文章，可以加个关注哦！
