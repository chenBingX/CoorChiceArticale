![image](http://upload-images.jianshu.io/upload_images/1869462-5adbfda2b652a9f9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)     

> Binder系列第一篇:[《从getSystemService()开始，开撸Binder通讯机制》http://www.jianshu.com/p/1050ce12bc1e](http://www.jianshu.com/p/1050ce12bc1e)  

> Binder系列第二篇:[《能用【白话文】来分析Binder通讯机制？》http://www.jianshu.com/p/fe816777f2cf](http://www.jianshu.com/p/fe816777f2cf)  

> Binder系列第三篇:[《Binder机制之一次响应的故事》http://www.jianshu.com/p/4fba927dce05](http://www.jianshu.com/p/4fba927dce05)   

> Binder系列第二篇已经上线！[我为什么选择用【白话文】来分析Binder通讯机制？- http://www.jianshu.com/p/fe816777f2cf](http://www.jianshu.com/p/fe816777f2cf)    



# 始于getSystemService()
类似下面的代码，相信各位已经写了100遍了！

```
val accessibilityManager: AccessibilityManager =
        getSystemService(Context.ACCESSIBILITY_SERVICE) as AccessibilityManager
```
很简单，我们通过getSystemService()获取到了系统服务代理AccessibilityManager，我们通过应用内的AccessibilityManager可以系统中真正的AccessibilityManager互动。怎么做到的呢？我们慢慢来看，先从这个服务是怎么获取到的开始吧。  

## getSystemService()实质
我们平时调用getSystemService()实际获取到的是在`SystemServiceRegistry`中注册的各种系统服务的代理，它们大多在注册的时候会通过`ServiceManager.getService()`来取得和对应Service交互的IBinder对象，然后创建Service代理。  

所以，`ServiceManager.getService()`应该才算是真正的起点吧。这个方法CoorChice这篇文章[《3分钟看懂Activity启动流程》：http://www.jianshu.com/p/9ecea420eb52](http://www.jianshu.com/p/9ecea420eb52) 中有提到过，在那里ActivityManagerNative通过`ServiceManager.getService("Activtiy")`获取到了和ActivityManager交互的IBinder对象，并使用它创建了应用程序的ActivityManagerProxy。就不多说，感兴趣的同学可以先看看那篇文章。

## 这是一张重要的图

![image](http://upload-images.jianshu.io/upload_images/1869462-0f63e8caa73cca50.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 还是从ServiceManager.getService()开始吧
上面这张图其实已经把整个流程描绘的很清楚了，流程不算长，接下来就根据这张图分析下。  

图是从红色箭头开始看的，然后你可以跟着箭头一步一梳理这个流程，箭头上都有数字的！然后就是哪些个圈圈，相同颜色的表示同一个对象。So Sweet!  

## 步骤1 开始创建IServiceManager
首先，既然是ServiceManager.getService()方法，那我们当然要毫不犹的直接看这个方法。
```
public static IBinder getService(String name) {
    try {
        // 先从缓存取
        IBinder service = sCache.get(name);
        if (service != null) {
            return service;
        } else {
            // 没有就去获取
            return getIServiceManager().getService(name);
        }
    } catch (RemoteException e) {
        Log.e(TAG, "error in getService", e);
    }
    return null;
}
```
可以看出，ServiceManager中会缓存用过的系统服务的IBinder。只有首次使用才去获取，非常科学。需要注意的是，是通过ServiceManager中的成员IServiceManager去获取的。  

接下来，我们需要看看ServiceManager的获取过程。进入getIServiceManager()方法。

```
private static IServiceManager getIServiceManager() {
    if (sServiceManager != null) {
        //确保sServiceManager在进程中是单例
        return sServiceManager;
    }
    
    // 第一次会去获取sServiceManager
    sServiceManager = ServiceManagerNative.asInterface(BinderInternal.getContextObject());
    return sServiceManager;
}
```
非常简单的一个方法。从中我们可以看出，IServiceManager在应用程序中是一个单例，它用于应用程序内管理Service，它实际是系统的ServiceManager的代理。  

在首次调用这个方法时，自然是要创建IServiceManager。通过`ServiceManagerNative.asInterface(BinderInternal.getContextObject())`这么简单的一句创建了。假象！这一句隐藏了很多玄机。我们接着探索。

## 步骤2 获取参数IBinder
按照代码执行顺序来，我们先看`ServiceManagerNative.asInterface()`方法的参数`BinderInternal.getContextObject()`干了些什么？  


手动档单击进入BinderInternal.java后可以看到，这是个native方法！  

![image](http://upload-images.jianshu.io/upload_images/1869462-e63adfd26d549a7e.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

```
public static final native IBinder getContextObject();
```

WTF？我好慌，而且很害怕！没JNI层源码怎么办？  

[《AndroidXRed》http://androidxref.com一个神奇的网站！](http://androidxref.com)  

这个网站可以查看Android从底层到上层的所有源码，并且支持多种搜索，基本上能实现手动挡单击跳转的伪效果。非常的屌！对于CoorChice这种没什么内存，不敢编译源码的低配电脑而言，简直是福音！ 

现在可以看源码了，CoorChice这里选择了`Lollipop - 5.0.0_r2`版本的源码。  

![image](http://upload-images.jianshu.io/upload_images/1869462-667bd3d5e95ec1e9.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

从这里开始，你需要了解一些c/c++和JNI的知识。快复习一下吧！

## 步骤3 在JNI中看IBinder的获取过程
上面那个方法的JNI是在这个文件中的`/frameworks/base/core/jni/android_util_Binder.cpp`。
 
```
//这些方法会通过动态注册的方式注册到BinderInternal.java中
static const JNINativeMethod gBinderInternalMethods[] = {
    //getContextObject()就是JNI函数android_os_BinderInternal_getContextObject()
    { "getContextObject", "()Landroid/os/IBinder;", (void*)android_os_BinderInternal_getContextObject },
    ...
};
```
如果我们要找`getContextObject()`这个名字的函数，你会发现找不见，因为这里采用了动态注册的方法来注册相关函数的。所以，我们会在上面的方法数组中发现一个字符串`"getContextObject"`。嗯，就是它了。它所对应的函数是`android_os_BinderInternal_getContextObject()`。

```
static jobject android_os_BinderInternal_getContextObject(JNIEnv* env, jobject clazz)
{
    //通过ProcessState单例获取handle句柄为0的IBinder
    sp<IBinder> b = ProcessState::self()->getContextObject(NULL);
    return javaObjectForIBinder(env, b);
}

```
这个函数很简单，但是暗藏玄机+1！通过ProcessState获取到了一个IBinder，并转换成Java层的IBinder返回给Java层。   

接下来看看是怎么通过ProcessState获取到这个IBinder的。

## 步骤4 获取ProcessState的函数self()
首先，从`ProcessState::self()`看来，ProcessState应该是个单例。我们到`self()`里验证下。
```
sp<ProcessState> ProcessState::self()
{
    Mutex::Autolock _l(gProcessMutex);
    if (gProcess != NULL) {
        // 有就直接返回
        return gProcess;
    }
    //没有就创建一个ProcessState
    gProcess = new ProcessState;
    return gProcess;
}
```
很简短的单例代码，首次调用直接new。  


下面看看ProcessState对象是怎么被构造的。

## 步骤5 ProcessState的创建

```
ProcessState::ProcessState()
     //打开“／dev／binder”Binder驱动文件，将设备文件描述符保存在mDriverFD中
    : mDriverFD(open_driver())
    , mVMStart(MAP_FAILED)
    , mManagesContexts(false)
    , mBinderContextCheckFunc(NULL)
    , mBinderContextUserData(NULL)
    , mThreadPoolStarted(false)
    , mThreadPoolSeq(1)
{
    ...
}
```
主要是初始化成员变量。其中`mDriverFD(open_driver())`是比较关键的一个环节，需要单独说一说。它通过调用`open_driver()`函数，打开了dev/binder驱动文件，并将文件描述符赋值给`mDriverFD`。那么，ProcessState作为持有Binder驱动文件描述符的对象，就拥有了和Binder驱动产生联系的资格。  

下面简单看一下`open_driver()`函数。

## 步骤6 简要分析open_driver()函数

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
    //告知驱动程序最多可以多少条线程处理事务
    result = ioctl(fd, BINDER_SET_MAX_THREADS, &maxThreads);
    ...
    return fd;
}
```
这个函数主要是负责打开`/dev/binder`文件，并配置一些和Binder驱动交互的参数，最后把获得的Binder驱动文件描述符fd返回。由于ProcessState在应用程序中是单例，所以保证了一个应用程序只打开一次`/dev/binder`。  

至于具体是怎么打开`/dev/binder`文件的，后面在详细说明。这里下跳过看下一步。

## 步骤7 通过getContextObject()获取的IBinder是什么？
ProcessState获取到后，就要调用它的`getContextObject()`函数来获取到一个IBinder对象了。
```
sp<IBinder> ProcessState::getContextObject(const sp<IBinder>& /*caller*/)
{ 
    //获取handle句柄为0的Binder
    return getStrongProxyForHandle(0);
}
```
这个函数看起来是直接用来获取handle为0的IBinder的。这个为handle为0的IBinder代表着谁呢？对，你可能没猜到！它代表了系统的ServiceManager。也就是通过这个handle为0的IBinder可以和ServiceManager交互。也就是说，这个IBinder是Client用来和系统的ServiceManager交互的。这里也先不提了。  

## 步骤8 ProcessState管理着IBinder
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
            ...
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
这段代码流程有点长啊，虽然CoorChice已经去掉了很大一部分了。  

![image](http://upload-images.jianshu.io/upload_images/1869462-e660d66796d007f3.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

这里会调用`lookupHandleLocked()`函数来获取一个handle_entry类型的指针。我们还是先看看这个函数吧。

```
ProcessState::handle_entry* ProcessState::lookupHandleLocked(int32_t handle)
{
    //mHandleToObject是一个Vector，用于存放handle_entry结构体
    //该结构体会保存handle对应的IBinder指针
    const size_t N=mHandleToObject.size();
    if (N <= (size_t)handle) {
        //没有handle对应的handle_entry就新建一个插入mHandleToObject中
        //此时handle_entry还没有数据
        handle_entry e;
        e.binder = NULL;
        e.refs = NULL;
        status_t err = mHandleToObject.insertAt(e, N, handle+1-N);
        if (err < NO_ERROR) return NULL;
    }
     //取出mHandleToObject中handle对应的handle_entry
    return &mHandleToObject.editItemAt(handle);
   
}
```
很短的函数，并且注释已经足够清晰了吧？不多说了，回到`getStrongProxyForHandle()`函数中。  

在根据传入的handle（我们分析这个流程中handle为0）获取到对应的handle_entry后，会检查其binder成员是否存在，不存在则创建，存在直接返回。  

可以看到，返回的IBinder实际是一个BpBinder类型的。这个东西也后面再说，总之它与Client发送数据到Service有关。


到此，`ServiceManagerNative.asInterface()`函数就成功获取到了一个IBinder对象作为参数。下面看看它用这个参数干了啥？

## 步骤9、10 创建ServiceManagerProxy
拿到IBinder对象后，我们回到Java中，进入`ServiceManagerNative.asInterface()`方法。你可以注意看一下上图中的红色圈，看看这个IBinder

```
static public IServiceManager asInterface(IBinder obj)
{
    ...
    //创建一个ServiceManagerProxy
    return new ServiceManagerProxy(obj);
}
```
反正最后就是使用刚刚获取到的IBinder创建了一个ServiceManagerProxy代理，用来在应用程序内代表系统的ServiceManager，并通过它和系统的ServiceManager交互。

```
public ServiceManagerProxy(IBinder remote) {
        mRemote = remote;
}
```
可以看到，ServiceManagerProxy的构造函数十分简单。注意，ServiceManagerProxy是ServiceManagerNative的一个内部类。  

实际上这个模式在CoorChice这篇文章[《3分钟看懂Activity启动流程》：http://www.jianshu.com/p/9ecea420eb52](http://www.jianshu.com/p/9ecea420eb52)中也是出现过的。  

好了，既然拿到代理ServiceManagerProxy了，我们该看看`getService()`方法是如何获取到相应的Service的IBinder的。

## 步骤11 通过IPC获取对应Service的IBinder

```
public IBinder getService(String name) throws RemoteException {
    // 准备数据包
    Parcel data = Parcel.obtain(); //用于发送的数据包
    Parcel reply = Parcel.obtain(); //用于响应的数据包
    data.writeInterfaceToken(IServiceManager.descriptor);
    //将需要的Service的名字放到数据包中
    data.writeString(name);
    //通过远程的Binder发送data到Binder驱动，并将响应保存到reply中
    mRemote.transact(GET_SERVICE_TRANSACTION, data, reply, 0);
    //从响应结果中获取可用于和Service交互的Binder
    IBinder binder = reply.readStrongBinder();
    reply.recycle();
    data.recycle();
    return binder;
}
```
在这个方法中，创建了两个Parcel数据包。data是用来发送数据的，reply是用来接收数据的。  

注意`mRemote.transact(GET_SERVICE_TRANSACTION, data, reply, 0)`这里调用了刚刚从底层返回的IBinder对象的`transact()`方法，意思是要发送数据了，并且把reply一起传过去准备接收数据。这个过程是阻塞式的。接着，当远程Service（这里指的是系统的ServiceManager）响应后，代码就可以接着走了，而这时reply中已经装了响应结果了。我们调用Parcel的`readStrongBinder()`方法来取出来自Service响应的IBinder对象。然后我们拿着这个IBinder对象就可以和对应的系统Service通过IPC机制交互了。


# 总结

> - 抽出空余时间写文章分享需要动力，还请各位看官动动小手点个赞，给我点鼓励😄
> - 我一直在不定期的创作新的干货，想要上车只需进到我的【个人主页】点个关注就好了哦。发车喽～

嗯，这篇文章从getSystemService()方法开始撸了系统Service的获取过程。当然这只是个开始，CoorChice留了很多坑，比如open("/dev/binder", O_RDWR)发生了什么，IBinder.transact()又发生了什么？还有IBinder是个啥？Parcel又是如何发挥作用的？等等一些列的谜团，后面的文章中CoorChice会一一拨开它们的迷雾。这只是个开始。  

![image](http://upload-images.jianshu.io/upload_images/1869462-9a67b8298f18839e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

大家可以先对着图捋一捋。  

功力有限，有错还请指出一起交流交流。


**看到这里的童鞋快奖励自己一口辣条吧！**    


  
    
    

>  #### 想要看CoorChice的更多文章，请点个关注哦！
