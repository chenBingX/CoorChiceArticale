![Android](http://upload-images.jianshu.io/upload_images/1869462-1ee9c9600c8ac2cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 背景介绍
从事开发到了一定阶段，想要提高就必须搞明白系统的一些工作原理。为什么？因为只有明白了这些，你才能针对平台的特性写出优质的代码。当遇到棘手的问题时，你才能更快速的结合系统原理去寻找最优解决方案。底层基础决定上层建筑。这个原理在开发中同样适用。我是提倡 **回归基础** 的。高级的功能总是由最基本的元件构成，就好比为数不多的元素构成了我们难以想象的丰富的物质世界一样。**只有掌握了最根本的内容，才能促使你爆发出难以想象的创造力来！**  

重视基础，回归基础。回到最初，去探寻灵感。  愿与君共勉✌️！  


# 一张图明白Activity的启动流程
![Activity启动流程](http://upload-images.jianshu.io/upload_images/1869462-882b8e0470adf85a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
本篇主要讲的是从一个App启动，到Activity执行onCreate()的流程。后面关于Activity的生命周期相信大家基本都耳熟能详了。  

上图中我把涉及到的类名方法命均列出来了，你可以看着流程，打开源码跟着过一遍。相信在过完一遍之后，在今后的开发中你会更加自信！

上图乍一看可能感觉有些眼花缭乱，但请不要惧怕。其实根本就没什么东西，你只需要从蓝色箭头开始看下去，会发现一下就看完了。在结合下面简要的分析，3分钟内你就能搞明白Activity的启动流程。
  
关于Activity的启动，我在[【惊天秘密！从Thread开始，揭露Android线程通讯的诡计和主线程的阴谋】http://www.jianshu.com/p/8862bd2b6a29](http://www.jianshu.com/p/8862bd2b6a29)  一文中有提到过。这篇文章主要讲的是Thread线程到底是个什么东西，以及Android中的消息机制。感兴趣可以点链接看一看。
## 一切从main()方法开始
Android中，一个应用程序的开始可以说就是从**ActivityThread.java**中的main()方法开始的。都是学过Java的人，想必也都知道Java的程序入口就是main()方法。从这点而言，我们可以把它想成是一个Java程序（注意，不是说Android是个Java程序哦）去理解。 

从上图可以看到，main()方法中主要做的事情有：
1. 初始化主线程的Looper、主Handler。并使主线程进入等待接收Message消息的无限循环状态。关于Android的Handler机制，可以参考一下我上面提到的文章：  
[【惊天秘密！从Thread开始，揭露Android线程通讯的诡计和主线程的阴谋】http://www.jianshu.com/p/8862bd2b6a29](http://www.jianshu.com/p/8862bd2b6a29)   
下面是main()方法中比较关键的代码：

```
public static void main(String[] args){
    ...
    Looper.prepareMainLooper(); 
    //初始化Looper
    ...
    ActivityThread thread = new ActivityThread();
    //实例化一个ActivityThread
    thread.attach(false);
    //这个方法最后就是为了发送出创建Application的消息
    ... 
    Looper.loop();
    //主线程进入无限循环状态，等待接收消息
}

```

2.调用attach()方法，主要就是为了发送出初始化Application的消息。这个流程说长不长，说短不短。下文会再捋一捋。

## 创建Application的消息是如何发送的呢？
上面提到过，ActivityThread的attach()方法最终的目的是发送出一条创建Application的消息——H.BIND_APPLICATION，到主线程的主Handler中。那我们来看看attach()方法干了啥。  
attach()关键代码：
```
public void attach(boolean system){
    ...
    final IActivityManager mgr = ActivityManagerNative.getDefault();  
    //获得IActivityManager实例，下面会看看它是个啥
    try {
        mgr.attachApplication(mAppThread);
         //看见没？关键啊。mAppThread这个参数下面也会说一下
    } catch (RemoteException ex) {
        throw ex.rethrowFromSystemServer();
    }
    ...
}

```
莫慌莫慌，下面看看上面出现的两个对象是个啥。
### IActivityManager mgr是个啥？
从上图也可以看到，IActivityManager是一个接口，当我们调用`ActivityManagerNative.getDefault()`获得的实际是一个代理类的实例——**ActivityManagerProxy**，这个东西实现了IActivityManager接口。打开源码你会发现，**ActivityManagerProxy**是ActivityManagerNative的一个内部类。可以看出，Android团队在设计的过程中是实践了**最小惊异原则**的，就是把相关的东西尽量放在一起。那么既然是个代理类，它究竟代理了谁？代码里看看喽。  
下面这个代码稍微有点绕啊！老哥，稳住！  
1. 先看ActivityManagerProxy的构造函数：
```
public ActivityManagerProxy(IBinder remote) {
        mRemote = remote;
}
```
这个构造函数非常的简单。首先它需要一个IBinder参数，然后赋值给**mRemote**变量。这个**mRemote**显然是ActivityManagerProxy的成员变量，对它的操作是由ActivityManagerProxy来代理间接进行的。这样设计的好处是保护了mRemote，并且能够在操作mRemote前执行一些别的事务，并且我们是以IActivityManager的身份来进行这些操作的！这就非常巧妙了。  

2. 那么这个构造函数是在那调用的呢？
```
static public IActivityManager asInterface(IBinder obj) {
    if (obj == null) {
        return null;
    }
    IActivityManager in =
        (IActivityManager)obj.queryLocalInterface(descriptor);
    //先检查一下有没有
    if (in != null) {
        return in;
    }
    ...
    return new ActivityManagerProxy(obj);
    //这个地方调用了构造函数
}
```
上面这个方法是ActivityManagerNative中的一个静态方法，它会调用到ActivityManagerProxy的构造方法。然而，这个静态方法也需要一个IBinder作为参数!老夫被绕晕了。但是不怕，咱们继续往找！
3. getDefault()获取到的静态常量gDefault
```
private static final Singleton<IActivityManager> gDefault = 
  new Singleton<IActivityManager>() {
    protected IActivityManager create() {
       IBinder b = ServiceManager.getService("activity");
       //重点啊！IBinder实例就是在这里获得的。
        ...
        IActivityManager am = asInterface(b);
        //调用了上面的方法。
        ...
        return am;
    }
};
```
这是ActivityManagerNative的静态常量，它是一个单例。在其中终于获得了前面一直在用的IBinder实例。
```
IBinder b = ServiceManager.getService("activity");
```
试着在上图中找到对应位置。  

这里是通过**ServiceManager**获取到**IBinder**实例的。如果你以前了解**AIDL**通讯流程的话。这可能比较好理解一点，这只是通过另一种方式获取**IBinder**实例罢了。获取**IBinder**的目的就是为了通过这个**IBinder**和**ActivityManager**进行通讯，进而**ActivityManager**会调度发送**H.BIND_APPLICATION**即初始化Application的Message消息。如果之前没接触过**Binder**机制的话，只需知道这个目的就行了。我后面会写一篇专门介绍Android中Binder机制的文章。当然，你也可以参考一下罗大的系列文章，写的很详细，非常的很赞！[【Android系统进程间通信Binder机制在应用程序框架层的Java接口源代码分析
】http://m.blog.csdn.net/article/details?id=6642463](http://m.blog.csdn.net/article/details?id=6642463)。 

4. 再来看看attachApplication(mAppThread)方法。
```
public void attachApplication(IApplicationThread app){
  ...
  mRemote.transact(ATTACH_APPLICATION_TRANSACTION, data, reply, 0);  
  ...
}
```
这个方法我在上图中也体现出来了。  

这个方法中上面这一句是关键。调用了IBinder实例的tansact()方法，并且把参数app(这个参数稍后就会提到)放到了data中，最终传递给ActivityManager。

现在，我们已经基本知道了IActivityManager是个什么东东了。其实最重要的就是它的一个实现类**ActivityManagerProxy**，它主要代理了内核中与**ActivityManager**通讯的**Binder**实例。下面再看看**ApplicationThread mAppThread**。

### ApplicationThread mAppThread又是个啥？
1. 在ActivityThread的成员变量中，你能够发现：
```
final ApplicationThread mAppThread = new ApplicationThread();
```
ApplicationThread是作为ActivityThread中的一个常量出现的。这表明系统不希望这个变量中途被修改，可见这个变量具有特定而十分重要的作用。
2. 我们看看他是啥。
```
private class ApplicationThread extends ApplicationThreadNative{
    ...
}
```
ApplicationThread是ActivityThread中的一个内部类，为什么没有单独出来写在别的地方呢？我觉得这也是对最小惊异原则的实践。因为ApplicationThread是专门真对这里使用的对象。
3. 它继承自ApplicationThreadNative，我们再看看它是个啥。
```
public abstract class ApplicationThreadNative extends Binder 
    implements IApplicationThread{
    ...
    //无参构造函数
    public ApplicationThreadNative() {
        //这是Binder的
        attachInterface(this, descriptor);
    }
    ...
}
```
那么很明显，ApplicationThread最终也是一个Binder！同时，由于实现了IApplicationThread接口，所以它也是一个IApplicationThread。以上这系对应关系你都可以在上图中找到。  

我们在ActivityThread中看到的ApplicationThread使用的构造函数是无参的，所以看上面无参构造函数都干了啥！

Binder的attachInterface(IInterface owner, String descriptor)方法没什么特别的，就是赋值了。
```
public void attachInterface(IInterface owner, String descriptor) {
    mOwner = owner;
    mDescriptor = descriptor;
}
```

4.那么IApplicationThread又是啥？老铁，走着！我们继续挖。
```
public interface IApplicationThread extends IInterface {
    ...
    String descriptor = "android.app.IApplicationThread"; 
    //留意下这个参数
    ...
}
```
好吧，这在上图中没有，挖的有点什么了。但是学习嘛，咱就看看喽。  

IApplicationThread是继承了IInterface的一个接口，我们需要关注一下里面的descriptor参数。后面会用它，它是一个标识，查询的时候很重要。  

好，我们终于知道attach()方法中出现的两个对象是啥了。ApplicationThread作为IApplicationThread的一个实例，承担了最后发送Activity生命周期、及其它一些消息的任务。也就是说，前面绕了一大圈，最后还是回到这个地方来发送消息。我擦！  

也许你会想，既然在ActivityThread中我们已经创建出了ApllicationThread的了，为什么还要绕这么弯路？，当然是为了让系统根据情况来控制这个过程喽，不然为什么要把ApplicationThread传到ActivityManager中呢？

### ActivityManagerService调度发送初始化消息
经过上面的辗转，ApplicationThread终于到了ActivityManagerService中了。请在上图中找到对应位置！  

从上图中可以看到，ActivityManagerService中有一这样的方法：
```
private final boolean attachApplicationLocked(IApplicationThread thread
, int pid) {
    ...
    thread.bindApplication();
    //注意啦！
    ...
}
```
ApplicationThread以IApplicationThread的身份到了ActivityManagerService中，经过一系列的操作，最终被调用了自己的bindApplication()方法，发出初始化Applicationd的消息。
```
public final void bindApplication(String processName, 
    ApplicationInfo appInfo,
    List<ProviderInfo> providers, 
    ComponentName instrumentationName,
    ProfilerInfo profilerInfo, 
    Bundle instrumentationArgs,
    IInstrumentationWatcher instrumentationWatcher,
    IUiAutomationConnection instrumentationUiConnection, 
    int debugMode,
    boolean enableBinderTracking, 
    boolean trackAllocation,
    boolean isRestrictedBackupMode, 
    boolean persistent, 
    Configuration config,
    CompatibilityInfo compatInfo, 
    Map<String, IBinder> services, 
    Bundle coreSettings){
    
    ...
    sendMessage(H.BIND_APPLICATION, data);
}
```
吓屎老纸！这么多参数。这明明很违反参数尽量要少的原则嘛！所以说，有的时候，开发过程中还是很难避免一些参数堆积的情况的。也不能一概而论。  

但是，这个地方，我们只要知道最后发了一条**H.BIND_APPLICATION**消息，接着程序开始了。  

## 收到初始化消息之后的世界
上面我们已经找到初始化Applicaitond的消息是在哪发送的了。现在，需要看一看收到消息后都发生了些什么。  

现在上图的H下面找到第一个消息：**H.BIND_APPLICATION**。一旦接收到这个消息就开始创建Application了。这个过程是在handleBindApplication()中完成的。看看这个方法。在上图中可以看到对应的方法。
```
private void handleBindApplication(AppBindData data) {
    ...
    mInstrumentation = (Instrumentation)
        cl.loadClass(data.instrumentationName.getClassName())
        .newInstance();
    //通过反射初始化一个Instrumentation仪表。后面会介绍。
    ...
    Application app = data.info.makeApplication(data.restrictedBackupMode, null);
    //通过LoadedApp命令创建Application实例
    mInitialApplication = app;
    ...
    mInstrumentation.callApplicationOnCreate(app);
    //让仪器调用Application的onCreate()方法
    ...
}
```
handleBindApplication()是一个很长的方法，但是我为各位看官精选出了上面这几句代码。对于本篇的主题来说，他们是至关重要的。上面短短的代码中出现了几个新对象。下面我会一一道来。

### Instrumentation仪表，什么鬼？
1.这个叫Instrumentation仪表的东西十分诡异，姑且翻译为仪器吧。字面上看不出任何它是干什么的线索。但是，我们可以打开文档看看喽。
> Instrumentation会在应用程序的任何代码运行之前被实例化，它能够允许你监视应用程序和系统的所有交互。

大概就这个意思啦。  

2.但是，从上面的代码我们可以看出，Instrumentation确实是在Application初始化之前就被创建了。那么它是如何实现监视应用程序和系统交互的呢？  

打开这个类你可以发现，最终Apllication的创建，Activity的创建，以及生命周期都会经过这个对象去执行。简单点说，就是把这些操作包装了一层。通过操作Instrumentation进而实现上述的功能。   

3.那么这样做究竟有什么好处呢？仔细想想。Instrumentation作为抽象，当我们约定好需要实现的功能之后，我们只需要给Instrumentation仪表添加这些抽象功能，然后调用就好。剩下的，不管怎么实现这些功能，都交给Instrumentation仪器的实现对象就好。啊！这是多态的运用。啊！这是依赖抽象，不依赖具体的实践。啊！这是上层提出需求，底层定义接口，即依赖倒置原则的践行。呵！抽象不过如此。   

从代码中可以看到，这里实例化Instrumentation的方法是反射！而反射的ClassName是来自于从ActivityManagerService中传过来的Binder的。套路太深！就是为了隐藏具体的实现对象。但是这样耦合性会很低。

4.好了，不瞎扯了。既然在说Instrumentation，那就看看最后调的callApplicationOnCreate()方法。
```
public void callApplicationOnCreate(Application app) {
    app.onCreate();
}
```
你没看错，它啥也没干。只是调用了一下Application的onCreate()方法。这就是为什么它能够起到监控的作用。  

在上图中你能够看到Instrumentation，以及它的交互过程。

### LoadedApk就是data.info哦！
关于它是怎么来的本篇就不说了，以后可能会介绍下。本篇就看流程就好。所以直接进去看它的makeApplication()干了啥，就把Application给创建了。
```
public Application makeApplication(boolean forceDefaultAppClass,
    Instrumentation instrumentation) {
    ...
    String appClass = mApplicationInfo.className;
    //Application的类名。明显是要用反射了。
    ...
    ContextImpl appContext = ContextImpl.createAppContext(mActivityThread
        , this);
    //留意下Context
    app = mActivityThread.mInstrumentation
        .newApplication( cl, appClass, appContext);
    //通过仪表创建Application
    ...
}
```
在这个方法中，我们需要知道的就是，在取得Application的实际类名之后，最终的创建工作还是交由Instrumentation去完成，就像前面所说的一样。  

值得留意的是，就像上图所标注的一样，当需要第二次获取Application时，同样只需要调用这个方法就好。“真是方便！”

### 现在把目光移回Instrumentation
看看newApplication()中是如何完成Application的创建的。
```
static public Application newApplication(Class<?> clazz
    , Context context) throws InstantiationException
    , IllegalAccessException
    , ClassNotFoundException {
        Application app = (Application)clazz.newInstance();
        //反射创建，简单粗暴
        app.attach(context);
        //关注下这里，Application被创建后第一个调用的方法。
        //目的是为了绑定Context。
        return app;
    }
```
我的天，绕了这么多，这Application可算是创建出来了。快给自己一个小红花吧！

## LaunchActivity
当Application初始化完成后，系统会更具Manifests中的配置的启动Activity发送一个Intent去启动相应的Activity。这个过程本篇先不提，下次再说。主要看流程！  

1. 直接的，H就收到了一条LAUNCH_ACTIVITY的消息。然后开始初始化Activity之旅。收到消息后，真正处理是在ActivityThread中的handleLaunchActivity()中进行的。是不是迫不及待的想要知道发生了啥？快在上图中找到对应的步骤吧！
```
private void handleLaunchActivity(ActivityClientRecord r
    , Intent customIntent
    , String reason) {
    ...
    Activity a = performLaunchActivity(r, customIntent);
    //妈蛋！又封装到另一个方法中创建了。
    ...
    if (a != null) {
        ...
        handleResumeActivity(r.token
        , false
        , r.isForward
        ,!r.activity.mFinished && !r.startsNotResumed
        , r.lastProcessedSeq, reason);
        //Activity创建成功就往onResume()走了！
        ...
    }
}
```
从上面的代码中可以看出...好吧，什么都看不出来！
2. 再走一个方法。
```
private Activity performLaunchActivity(ActivityClientRecord r
    , Intent customIntent) {
    ...
    activity = mInstrumentation.newActivity(
         cl, component.getClassName(), r.intent);
    //通过仪表来创建Activity
    ...
     Application app = r.packageInfo.makeApplication(false
     , mInstrumentation);
     //前面说过，是在获取Application
    ...
    activity.attach(appContext
        , this
        , getInstrumentation()
        , r.token
        ,.ident
        , app
        , r.intent
        , r.activityInfo
        , title
        , r.parent
        , r.embeddedID
        , r.lastNonConfigurationInstances
        , config
        ,r.referrer
        , r.voiceInteractor
        , window);
    //方法怪出现！
    ...
    if (r.isPersistable()) {
        mInstrumentation.callActivityOnCreate(
          activity, r.state, r.persistentState);
    } else {
        mInstrumentation.callActivityOnCreate(activity, r.state);
    }
    //根据是否可持久化选择onCreate()方法。
    ...
}
```
这个方法内容较多，我们一个个看。
1. 
```
activity = mInstrumentation.newActivity(
         cl, component.getClassName(), r.intent);
```
正如前面所说，Activity、Application的创建及生命周期都被承包给Instrumentation仪表了。所以由它来负责。看看Instrumentation干了啥。
```
public Activity newActivity(ClassLoader cl, String className,
            Intent intent)
            throws InstantiationException
            , IllegalAccessException,
            ClassNotFoundException {
        return (Activity)cl.loadClass(className).newInstance();
        //真的没干啥。反射实例化Activity而已
    }
```
就是反射出一个Activity而已。  

2. 
```
if (r.isPersistable()) {
        mInstrumentation.callActivityOnCreate(
          activity, r.state, r.persistentState);
    } else {
        mInstrumentation.callActivityOnCreate(activity, r.state);
    }
```
根据是否可持久化选择Activity的onCreate()方法。同样是通过Instrumentation仪表来执行onCreate()的。它两分别对应的onCreate()方法为：
```
onCreate(icicle, persistentState);
//可获得持久化数据
```
和
```
onCreate(icicle);
//平时重写的最多的。
```

中间两个方法留意一下就好，就不在解释的，感兴趣的点源码看看。

到此，Activity就跑起来了！怎么样？是不是并不复杂。


# 总结
本篇就到此结束了。本篇主要流程是从Application创建开始，到第一个Activity onCreate()结束的。这了流程也不算长，关键是结合上面的图来看。重点环节我都用不同的颜色标记出来了。  

看到这里的同学奖励自己一包辣条吧！



# 参考链接
1. [【Android系统进程间通信Binder机制在应用程序框架层的Java接口源代码分析
】http://m.blog.csdn.net/article/details?id=6642463](http://m.blog.csdn.net/article/details?id=6642463)

2. [【Instrumentation API】https://developer.android.com/reference/android/app/Instrumentation.html](https://developer.android.com/reference/android/app/Instrumentation.html)
3. [【Activity启动流程（下）】http://www.jianshu.com/p/3cf90c633bb1](http://www.jianshu.com/p/3cf90c633bb1)
4. [【Android学习——ActivityManager与Proxy模式的运用】http://www.cnblogs.com/bastard/archive/2012/05/25/2517522.html](http://www.cnblogs.com/bastard/archive/2012/05/25/2517522.html)
5. [【ActivityManager API】https://developer.android.com/reference/android/app/ActivityManager.html](https://developer.android.com/reference/android/app/ActivityManager.html)

  

**感谢你的阅读。如果你觉得对你有用的话，记得给CoorChice点个赞，添加下关注哦，谢谢！**
