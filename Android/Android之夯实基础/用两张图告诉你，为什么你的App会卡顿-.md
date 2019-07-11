![Cover](http://upload-images.jianshu.io/upload_images/1869462-e45f4cf10981b323.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 有什么料？
从这篇文章中你能获得这些料：
- 知道setContentView()之后发生了什么？
- 知道Android究竟是如何在屏幕上显示我们期望的画面的？
- 对Android的视图架构有整体把握。
- 学会从根源处分析画面卡顿的原因。
- 掌握如何编写一个流畅的App的技巧。
- 从源码中学习Android的细想。
- 收获两张自制图，帮助你理解Android的视图架构。

![link](http://upload-images.jianshu.io/upload_images/1869462-59ab533d5276864e.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 从setContentView()说起

```
public class AnalyzeViewFrameworkActivity extends Activity {
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_analyze_view_framwork);
  }
}
```
上面这段代码想必Androider们大都已经不能再熟悉的更多了。但是你知道这样写了之后发生什么了吗？这个布局到底被添加到哪了？我的天，知识点来了！ 

可能很多同学也知道这个布局是被放到了一个叫做DecorView的父布局里，但是我还是要再说一遍。且看下图✌️
![image](http://upload-images.jianshu.io/upload_images/1869462-26584be9906690ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
这个图可能和伙伴们在书上或者网上常见的不太一样，为什么不太一样呢？因为是我自己画的，哈哈哈...    

下面就来看着图捋一捋Android最基本的视图框架。

## PhoneWindow
估计很多同学都知道，每一个Activity都拥有一个Window对象的实例。这个实例实际是PhoneWindow类型的。那么PhoneWindow从名字很容易看出，它应该是Window的儿子（即子类）！  


**知识点：每一个Activity都有一个PhoneWindow对象。**  

那么，PhoneWindow有什么用呢？它在Activity充当什么角色呢？下面我就姑且把PhoneWindow等同于Window来称呼吧。  
![image](http://upload-images.jianshu.io/upload_images/1869462-2bf58e5125348273.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Window从字面看它是一个窗口，意思和PC上的窗口概念有点像。但也不是那么准确。看图说。可以看到，我们要显示的布局是被放到它的属性mDecor中的，这个mDecor就是DecorView的一个实例。下面会专门撸DecorView，现在先把关注点放到Window上。Window还有一个比较重要的属性mWindowManager，它是WindowManager(这是个接口)的一个实现类的一个实例。我们平时通过getWindowManager()方法获得的东西就是这个mWindowManager。顾名思义，它是Window的管理者，负责管理着窗口及其中显示的内容。它的实际实现类是WindowManagerImpl。可能童鞋们现在正在PhoneWindow中寻找着这个mWindowManager是在哪里实例化的，是不是上下来回滚动着这个类都找不见？STOP！mWindowManager是在它爹那里就实例化好的。下面代码是在`Window.java`中的。  
```
public void setWindowManager(WindowManager wm, 
    IBinder appToken, 
    String appName, 
    boolean hardwareAccelerated) {
        ...
        if (wm == null) {
            wm = (WindowManager)mContext.getSystemService(Context.WINDOW_SERVICE);
            //获取了一个WindowManager
        }
        mWindowManager = ((WindowManagerImpl)wm).createLocalWindowManager(this);
        //通过这里我们可以知道，上面获取到的wm实际是WindowManagerImpl类型的。
    }
```
通过上面的介绍，我们已经知道了Window中有负责承载布局的DecorView，有负责管理的WindowManager（事实上它只是个代理，后面会讲它代理的是谁）。

## DecorView
前面提到过，在Activity的onCreate()中通过setContentView()设置的布局实际是被放到DecorView中的。我们在图中找到DecorView。    

从图中可以看到，DecorView继承了FrameLayout，并且一般情况下，它会在先添加一个预设的布局。比如DecorCaptionView，它是从上到下放置自己的子布局的，相当于一个LinearLayout。通常它会有一个标题栏，然后有一个容纳内容的mContentRoot，这个布局的类型视情况而定。我们希望显示的布局就是放到了mContentRoot中。  

**知识点：通过setContentView()设置的布局是被放到DecorView中，DecorView是视图树的最顶层。**  

## WindowManager
前面已经提到过，WindowManager在Window中具有很重要的作用。我们先在图中找到它。这里需要先说明一点，在PhoneWindow中的mWindowManager实际是WindowManagerImpl类型的。WindowManagerImpl自然就是接口WindowManager的一个实现类喽。这一点是我没有在图中反映的。  

WindowManager是在Activity执行attach()时被创建的，attach()方法是在onCreate()之前被调用的。关于Activity的创建可以看看我的这篇：[**【可能是史上最简单的！一张图3分钟让你明白Activity启动流程，不看后悔！http://www.jianshu.com/p/9ecea420eb52】**](http://www.jianshu.com/p/9ecea420eb52)。  
**Activity.java**

```
final void attach(Context context, ActivityThread aThread,
    Instrumentation instr, IBinder token, int ident,
    Application application, Intent intent, ActivityInfo info,
    CharSequence title, Activity parent, String id,
    NonConfigurationInstances lastNonConfigurationInstances,
    Configuration config, String referrer, IVoiceInteractor voiceInteractor,
    Window window){
        ...
        mWindow = new PhoneWindow(this, window);
        //创建Window
        ...
        mWindow.setWindowManager(
         (WindowManager)context.getSystemService(Context.WINDOW_SERVICE),
         mToken, mComponent.flattenToString(),
         (info.flags & ActivityInfo.FLAG_HARDWARE_ACCELERATED) != 0);
        //注意！这里就是在创建WindowManager。
        //这个方法在前面已经说过了。
        if (mParent != null) {
           mWindow.setContainer(mParent.getWindow());
        }
        mWindowManager = mWindow.getWindowManager();
            }
```  

继续看图。WindowManagerImpl持有了PhoneWindow的引用，因此它可以对PhoneWindow进行管理。同时它还持有一个非常重要的引用mGlobal。这个mGlobal指向一个WindowManagerGlobal类型的单例对象，这个单例每个应用程序只有唯一的一个。在图中，我说明了WindowManagerGlobal维护了本应用程序内所有Window的DecorView，以及与每一个DecorView对应关联的ViewRootImpl。这也就是为什么我前面提到过，WindowManager只是一个代理，实际的管理功能是通过WindowManagerGlobal实现的。我们来看个源码的例子就比较清晰了。开始啦！  
![image](http://upload-images.jianshu.io/upload_images/1869462-f784ff4183bd92c5.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

**WimdowManagerImpl.java**

```
public void addView(@NonNull View view, @NonNull ViewGroup.LayoutParams params) {
    ...
    mGlobal.addView(view, params, mContext.getDisplay(), mParentWindow);
    //实际是通过WindowManagerGlobal实现的。
}
```
从上面的代码可以看出，WindowManagerImpl确实只是WindowManagerGlobal的一个代理而已。同时，上面这个方法在整个Android的视图框架流程中十分的重要。我们知道，在Activity执行onResume()后界面就要开始渲染了。原因是在onResume()时，会调用WindowManager的addView()方法(实际最后调用的是WindowManagerGlobal的addView()方法)，把视图添加到窗口上。结合我的这篇[**【可能是史上最简单的！一张图3分钟让你明白Activity启动流程，不看后悔！http://www.jianshu.com/p/9ecea420eb52】**](http://www.jianshu.com/p/9ecea420eb52)看，可以帮助你更好的理解Android的视图框架。  
**ActivityThread.java**

```
final void handleResumeActivity(IBinder token,
    boolean clearHide, boolean isForward, boolean reallyResume, int seq, String reason) {
    ...
    ViewManager wm = a.getWindowManager();
    //获得WindowManager，实际是WindowManagerImpl
    ...
    wm.addView(decor, l);
    //添加视图
    ...
    wm.updateViewLayout(decor, l);
    //需要刷新的时候会走这里
    ...
}
```
从上面可以看到，当Activity执行onResume()的时候就会添加视图，或者刷新视图。需要解释一点：**WindowManager实现了ViewManager接口。**  

如图中所说，WindowManagerGlobal调用addView()的时候会把DecorView添加到它维护的数组中去，并且会创建另一个关键且极其重要的ViewRootImpl(这个必须要专门讲一下)类型的对象，并且也会把它存到一个数组中维护。  
**WindowManagerGlobal.java**
```
public void addView(View view, ViewGroup.LayoutParams params,
    Display display, Window parentWindow) {
    ...
    root = new ViewRootImpl(view.getContext(), display);
    //重要角色登场
    view.setLayoutParams(wparams);
    mViews.add(view);
    mRoots.add(root);
    //保存起来维护
    mParams.add(wparams);
    ...
    root.setView(view, wparams, panelParentView);
    //设置必要属性view是DecorView，panelParentView是PhoneWindow
    ...
}
```
可以看出ViewRootImpl是在Activity执行onResume()的时候才被创建的，并且此时才把DecorView传进去让它管理。  

**知识点：WindowManager是在onCreate()时被创建。它对窗口的管理能力实际是通过WindowManagerGlobal实现的。在onResume()是视图才通过WindowManager被添加到窗口上。**  

## ViewRootImpl
ViewRootImpl能够和系统的WindowManagerService进行交互，并且管理着DecorView的绘制和窗口状态。非常的重要。赶紧在图中找到对应位置吧！  

ViewRootImpl并不是一个View，而是负责管理视图的。它配合系统来完成对一个Window内的视图树的管理。从图中也可以看到，它持有了DecorView的引用，并且视图树它是视图树绘制的起点。因此，ViewRootImpl会稍微复杂一点，需要我们更深入的去了解，在图中我标出了它比较重要的组成Surface和Choreographer等都会在后面提到。  

到此，我们已经一起把第一张图撸了一遍了，现在童鞋们因该对Android视图框架有了大致的了解。下面将更进一步的去了解Android的绘制机制。

# App总是卡顿到底是什么原因？
**下面将会详细的讲解为什么我们设置的视图能够被绘制到屏幕上？这中间究竟隐藏着怎样的离奇？看完之后，你自然就能够从根源知道为什么你的App会那么卡，以及开始有思路着手解决这些卡顿。**

![image](http://upload-images.jianshu.io/upload_images/1869462-c71f7527c48dac86.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

同样用一张图来展示这个过程。由于Android绘制机制确实有点复杂，所以第一眼看到的时候你的内心中可能蹦腾了一万只草泥马😂。不要怕！我们从源头开始，一点一点的梳理这个看似复杂的绘制机制。为什么说看似复杂呢？因为这个过程只需要几分钟。Just Do It！ 

## CPU、GPU是搞什么鬼的？
整天听到CPU、GPU的，你知道他们是干什么的吗？这里简单的提一下，帮助理解后面的内容。  

在Android的绘制架构中，CPU主要负责了视图的测量、布局、记录、把内容计算成Polygons多边形或者Texture纹理，而GPU主要负责把Polygons或者Textture进行Rasterization栅格化，这样才能在屏幕上成像。在使用硬件加速后，GPU会分担CPU的计算任务，而CPU会专注处理逻辑，这样减轻CPU的负担，使得整个系统效率更高。  
![image](http://upload-images.jianshu.io/upload_images/1869462-5378b48208c472f0.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## RefreshRate刷新率和FrameRate帧率
RefreshRate刷新率是屏幕每秒刷新的次数，是一个与硬件有关的固定值。在Android平台上，这个值一般为60HZ，即屏幕每秒刷新60次。  

FrameRate帧率是每秒绘制的帧数。通常只要帧数和刷新率保持一致，就能够看到流畅的画面。在Android平台，我们应该尽量维持60FPS的帧率。但有时候由于视图的复杂，它们可能就会出现不一致的情况。  

![image](http://upload-images.jianshu.io/upload_images/1869462-09ee19216a8e9697.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

如图，当帧率小于刷新率时，比如图中的30FPS < 60HZ，就会出现相邻两帧看到的是同一个画面，这就造成了卡顿。这就是为什么我们总会说，要尽量保证一帧画面能够在16ms内绘制完成，就是为了和屏幕的刷新率保持同步。  

下面将会介绍Android是如何来确保刷新率和帧率保持同步的。


## Vsync(垂直同步)是什么？
你可能在游戏的设置中见过Vsync，开启它通常能够提高游戏性能。在Android中，同样使用Vsync垂直同步来提高显示性能。它能够使帧率FrameRate和硬件的RefreshRate刷新强制保持一致。

### HWComposer与Vsync不得不说的事
看图啦看图啦。首先在最左边我们看到有个叫HWComposer的类，这是一个c++编写的类。它Android系统初始化时就被创建，然后开始配合硬件产生Vsync信号，也就是图中的HW_Vsync信号。当然它不是一直不停的在产生，这样会导致Vsync信号的接收者不停的接收到绘制、渲染命令，即使它们并不需要，这样会带来严重的性能损耗，因为进行了很多无用的绘制。所以它被设计设计成能够唤醒和睡眠的。这使得HWComposer在需要时才产生Vsync信号(比如当屏幕上的内容需要改变时)，不需要时进入睡眠状态(比如当屏幕上的内容保持不变时，此时屏幕每次刷新都是显示缓冲区里没发生变化的内容)。  

如图，Vsync的两个接收者，一个是SurfaceFlinger(负责合成各个Surface)，一个是Choreographer(负责控制视图的绘制)。我们稍后再介绍，现在先知道它们是干什么的就行了。


### Vsync offset机制
为了提高效率，尽量减少卡顿，在Android 4.1时引入了Vsync机制，并在随后的4.4版本中加入Vsync offset偏移机制。
![image](http://upload-images.jianshu.io/upload_images/1869462-fbd75bb6619ebee0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

图1. 为4.1时期的Vsync机制。可以看到，当一个Vsync信号到来时，SurfaceFlinger和UI绘制进程会同时启动，导致它们竞争CPU资源，而CPU分配资源会耗费时间，着降低系统性能。同时当收到一个Vsync信号时，第N帧开始绘制。等再收到一个Vsync信号时，第N帧才被SurfaceFlinger合成。而需要显示到屏幕上，需要等都第三个Vsync信号。这是比较低效率。于是才有了图2. 4.4版本加入的Vsync offset机制。  

图2. Google加入Vsync offset机制后，原本的HW_Vsync信号会经过DispSync会分成Vsync和SF_Vsync两个虚拟化的Vsync信号。其中Vsync信号会发送到Choreographer中，而SF_Vsync会发送到SurfaceFlinger中。理论上只要phase_app和phase_sf这两个偏移参数设置合理，在绘制阶段消耗的时间控制好，那么画面就会像图2中的前几帧那样有序流畅的进行。理想总是美好的。实际上很难一直维持这种有序和流畅，比如frame_3是比较复杂的一帧，它的绘制完成的时间超过了SurfaceFlinger开始合成的时间，所以它必须要等到下一个Vsync信号到来时才能被合成。这样便造成了一帧的丢失。但即使是这样，如你所见，加入了Vsync offset机制后，绘制效率还是提高了很多。  

从图中可以看到，Vsync和SF_Vsync的偏移量分别由phase_app和phase_sf控制，这两个值是可以调节的，默认为0，可为负值。你只需要找到BoardConfig.mk文件，就可以对这两个值进行调节。

## 回到ViewRootImpl
前面介绍了几个关键的概念，现在我们回到ViewRootImpl中去，在图中找到ViewRootImpl的对应位置。

前面说过，ViewRootImpl控制着一个Window中的整个视图树的绘制。那它是如何进行控制的呢？一次绘制究竟是如何开始的呢？  

![image](http://upload-images.jianshu.io/upload_images/1869462-f1a96942cd7f1f93.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

在ViewRootImpl创建的时候，会获取到前面提到过过的一个关键对象Choreographer。Choreographer在一个线程中仅存在一个实例，因此在UI线程只有一个Choreographer存在。也就说，通常情况下，它相当于一个应用中的单例。  

在ViewRootImpl初始化时，会实现一个Choreographer.FrameCallback（这是一个Choreographer中的内部类），并向Choreographer中post。顾名思义，FrameCallback会在每次接收到Vsync信号时被回调。   
**Choreographer.java**
```
public interface FrameCallback {
    public void doFrame(long frameTimeNanos);
    //一旦注册到CallbackQueue中，那么
    //每次Choreographer接收到Vsync信号时都会回调。
    }
```
FrameCallback一旦被注册，那么每次收到Vsync信号时它都会被回调。利用它，我们可以实现会帧率的监听。  

**ViewRootImpl.java**

```
//这个方法只有在ViewRootImpl初始化时才会被调用
private void profileRendering(boolean enabled) {
    ...
    mRenderProfiler = new Choreographer.FrameCallback() {
    @Override
    public void doFrame(long frameTimeNanos) {
        ...
        scheduleTraversals();
        //请求一个Vsync信号，后面还会提到这个方法
        mChoreographer.postFrameCallback(mRenderProfiler);
        //每次回调时，重新将FrameCallback post到Choreographer中
        ...
    }
    };
    ...
    mChoreographer.postFrameCallback(mRenderProfiler);
    //将FrameCallback post到Choreographer中
    ...
}
```
上面代码出现了一个重要方法scheduleTraversals()。下面我们看看它究竟为何重要。
**ViewRootImpl.java**
```
void scheduleTraversals() {
    ...
    mChoreographer.postCallback(
        Choreographer.CALLBACK_TRAVERSAL, mTraversalRunnable, null);
    //向Choreographer中post一个TraversalRunnable
    //这又是一个十分重要的对象
    ...
    }
```
可以看出scheduleTraversals()每次调用时会向Choreographer中post一个TraversalRunnable，它会促使Choreographer去请求一个Vsync信号。所以这个方法的作用就是用来请求一次Vsync信号刷新界面的。事实上，你可以看到，在**invalidate()、requestLayout()**等操作中，都能够看到它被调用。原因就是这些操作需要刷新界面，所以需要请求一个Vsync信号来出发新界面的绘制。  

**ViewRootImpl.java**
```
final class TraversalRunnable implements Runnable {
    @Override
    public void run() {
        doTraversal();
        //开始遍历视图树，这意味着开始绘制一帧内容了
    }
}
```
从图中可以看到，每当doTraversal()被调用时，一系列的测量、布局和绘制操作就开始了。在绘制时，会通过Surface来获取一个Canvas内存块交给DecorView，用于视图的绘制。整个View视图的内容都是被绘制到这个Canvas中。  

## Choreographer中的风起云涌
前面反复提到向Choreographer中post回调，那么post过去发生了些什么呢？从图中可以看到，所有的post操作最终都进入到postCallbackDelayedInternal()中。  

![image](http://upload-images.jianshu.io/upload_images/1869462-c247974ef718eee6.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

**Choreographer.java**
```
private void postCallbackDelayedInternal(int callbackType,
    Object action, Object token, long delayMillis) {
    ...
    synchronized (mLock) {
        final long now = SystemClock.uptimeMillis();
        final long dueTime = now + delayMillis;
        mCallbackQueues[callbackType].addCallbackLocked(dueTime, action, token);
        //将Callback添加到CallbackQueue[]中
        if (dueTime <= now) {
            scheduleFrameLocked(now);
            //如果回调时间到了，请求一个Vsync信号
            //在接收到后会调用doFrame()回调这个Callback。
        } else {
            Message msg = mHandler.obtainMessage(MSG_DO_SCHEDULE_CALLBACK, action);
            msg.arg1 = callbackType;
            msg.setAsynchronous(true);
            //异步消息，避免被拦截器拦截
            mHandler.sendMessageAtTime(msg, dueTime);
            //如果还没到回调的时间，向FrameHandelr中发送
            //MSG_DO_SCHEDULE_CALLBACK消息
        }
    }
    ...
}
```
上面这段代码会把post到Choreographer中的Callback添加到Callback[]中，并且当它因该被回调时，请求一个Vsync信号，在接收到下一个Vsync信号时回调这个Callback。如果没有到回调的时间，则向FrameHandler中发送一个MSG_DO_SCHEDULE_CALLBACK消息，但最终还是会请求一个Vsync信号，然后回调这个Callback。  


**简单提一下CallbackQueue：**简单说一下CallbackQueue。它和MessageQueue差不多，都是单链表结构。在我的这篇[**【惊天秘密！从Thread开始，揭露Android线程通讯的诡计和主线程的阴谋http://www.jianshu.com/p/8862bd2b6a29】**](http://www.jianshu.com/p/8862bd2b6a29)文章中，你能够看到更多关于MessageQueue和Handler机制的内容。不同的是它同时还是一个一维数组，下标表示Callback类型。事实上，算上每种类型的单链表结构，它更像是二维数组的样子。简单点描述，假设有一个MessageQueue[]数组，里面存了几个MessageQueue。来看看它的创建你可能就明白，它是在Choreographer初始化时创建的。

```
private Choreographer(Looper looper) {
    mCallbackQueues = new CallbackQueue[CALLBACK_LAST + 1];
    //CALLBACK_LAST值为3。
    for (int i = 0; i <= CALLBACK_LAST; i++) {
        mCallbackQueues[i] = new CallbackQueue();
    }
}
```


现在来看看前面代码中调用的scheduleFrameLocked()是如何请求一个Vsync信号的。

```
private void scheduleFrameLocked(long now) {
    ...
    //先判断当前是不是在UI线程
    if (isRunningOnLooperThreadLocked()) {
        scheduleVsyncLocked();
        //是UI线程就请求一个Vsync信号
    } else {
        Message msg = mHandler.obtainMessage(MSG_DO_SCHEDULE_VSYNC);
        msg.setAsynchronous(true);
        mHandler.sendMessageAtFrontOfQueue(msg);
        //不在UI线程向FrameHandler发送一个MSG_DO_SCHEDULE_VSYNC消息
        //来请求一个Vsync信号
    }
}

private void scheduleVsyncLocked() {
    mDisplayEventReceiver.scheduleVsync();
    //通过DisplayEventReceiver请求一个Vsync信号
    //这是个恨角色，待会儿会聊聊它。
    //MSG_DO_SCHEDULE_VSYNC消息也是通过调用这个方法请求Vsync信号的。
}
```
上面我们提到过，Choreographer在一个线程中只有一个。所以，如果在其它线程，需要通过Handler来切换到UI线程，然后再请求Vsync信号。  

下面看看刚刚出场的mDisplayEventReceiver是个什么鬼？  
![image](http://upload-images.jianshu.io/upload_images/1869462-8a74259e1740f738.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

```
private final class FrameDisplayEventReceiver extends DisplayEventReceiver
    implements Runnable {
    
    //这个方法用于接收Vsync信号
    public void onVsync(){
        ...
        Message msg = Message.obtain(mHandler, this);
        msg.setAsynchronous(true);
        mHandler.sendMessageAtTime(msg, timestampNanos / TimeUtils.NANOS_PER_MS);
        //这里并没有设置消息的类型
        //其实就是默认为0，即MSG_DO_FRAME类型的消息
        //它其实就是通知Choreographer开始回调CallbackQueue[]中的Callback了
        //也就是开始绘制下一帧的内容了
    }
    
    //这个方法是在父类中的，写在这方便看
    public void scheduleVsync() {
        ...
        nativeScheduleVsync(mReceiverPtr);
        //请求一个Vsync信号
    }
}
```
这给类功能比较明确，而且很重要！  

![image](http://upload-images.jianshu.io/upload_images/1869462-3c314d0dd7776b65.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

上面一直在说向FrameHandler中发消息，搞得神神秘秘的。接下来就来看看FrameHandler本尊。请在图中找到对应位置哦。

```
private final class FrameHandler extends Handler {
    public FrameHandler(Looper looper) {
        super(looper);
    }

    @Override
    public void handleMessage(Message msg) {
        switch (msg.what) {
            case MSG_DO_FRAME:
                //开始回调Callback，以开始绘制下一帧内容
                doFrame(System.nanoTime(), 0);
                break;
            case MSG_DO_SCHEDULE_VSYNC:
                //请求一个Vsync信号
                doScheduleVsync();
                break;
            case MSG_DO_SCHEDULE_CALLBACK:
                //实际也是请求一个Vsync信号
                doScheduleCallback(msg.arg1);
                break;
            }
        }
    }
```
FrameHandler主要在UI线程处理3种类型的消息。
- MSG_DO_FRAME：值为0。当接收到一个Vsync信号时会发送该种类型的消息，然后开始回调CallbackQueue[]中的Callback。比如上面说过，在ViewRootImpl有两个重要的Callback，FrameCallback（请求Vsync并再次注册回调）和TraversalRunnable（执行doTraversal()开始绘制界面）频繁被注册。
- MSG_DO_SCHEDULE_VSYNC：值为1。当需要请求一个Vsync消息（即屏幕上的内容需要更新时）会发送这个消息。接收到Vsync后，同上一步。
- MSG_DO_SCHEDULE_CALLBACK：值为2。请求回调一个Callback。实际上会先请求一个Vsync信号，然后再发送MSG_DO_FRAME消息，然后再回调。  

FrameHandler并不复杂，但在UI的绘制过程中具有重要的作用，所以一定要结合图梳理下这个流程。  

## SurfaceFlinger和Surface简单说
在介绍Vsync的时候，我们可能已经看到了，现在Android系统会将HW_VSYNC虚拟化为两个Vsync信号。一个是VSYNC，被发送给上面一直在讲的Choreographer，用于触发视图树的绘制渲染。另一个是SF_VSYNC，被发送给我接下来要讲的SurfaceFlinger，用于触发Surface的合成，即各个Window窗口画面的合成。接下来我们就简单的看下SurfaceFlinger和Surface。由于这部分基本是c++编写的，我着重讲原理。   

### 隐藏在背后的Surface
平时同学们都知道，我们的视图需要被绘制。那么它们被绘制到那了呢？也许很多童鞋脑海里立即浮现出一个词：Canvas。但是，～没错！就是绘制到了Canvas上。那么Canvas又是怎么来的呢？是的，它可以New出来的。但是前面提到过，我们Window中的视图树都是被绘制到一个由Surface提供的Canvas上。忘了的童鞋面壁思过😄。  

![image](http://upload-images.jianshu.io/upload_images/1869462-4fe3db3fe4a20ca7.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

Canvas实际代表了一块内存，用于储存绘制出来的数据。在Canvas的构造器中你可以看到：

```
public Canvas() {
    ...
    mNativeCanvasWrapper = initRaster(null);
    //申请一块内存，并且返回该内存的一个long类型的标记或者索引。
    ...
}
```
可以看到，Canvas实际主要就是持有了一块用于绘制的内存块的索引`long mNativeCanvasWrapper`。每次绘制时就通过这个索引找到对应的内存块，然后将数据绘制到内存中。比如：

```
public void drawRect(@NonNull RectF rect, @NonNull Paint paint) {
    native_drawRect(mNativeCanvasWrapper,
        rect.left, rect.top, rect.right, rect.bottom, paint.getNativeInstance());
    //在mNativeCanvasWrapper标记的内存中绘制一个矩形。
    }
```
简单的说一下。Android绘制图形是通过图形库Skia(主要针对2D)或OpenGL(主要针对3D)进行。图形库是个什么概念？就好比你在PC上用画板画图，此时画板就相当于Android中的图形库，它提供了一系列标准化的工具供我们画图使用。比如我们drawRect()实际就是操作图形库在内存上写入了一个矩形的数据。  

扯多了，我们继续回到Surface上。当ViewRootImpl执行到draw()方法（即开始绘制图形数据了），会根据是否开启了硬件（从Android 4.0开始默认是开启的）加速来决定是使用CPU软绘制还是使用GPU硬绘制。如果使用软绘制，图形数据会绘制在Surface默认的CompatibleCanvas上(和普通Canvas的唯一区别就是对Matrix进行了处理，提高在不同设备上的兼容性)。如果使用了硬绘制，图形数据会被绘制在DisplayListCanvas上。DisplayListCanvas会通过GPU使用openGL图形库进行绘制，因此具有更高的效率。  

前面也简单说了一下，每一个Window都会有一个自己的Surface，也就是说一个应用程序中会存在多个Surface。通过上面的讲解，童鞋们也都知道了Surface的作用就是管理用于绘制视图树的Canvas的。这个Surface是和SurfaceFlinger共享，从它实现了Parcelable接口也可以才想到它会被序列化传递。事实上，Surface中的绘制数据是通过匿名共享内存的方式和SurfaceFlinger共享的，这样SurfaceFlinger可以根据不同的Surface，找到它所对应的内存区域中的绘制数据，然后进行合成。

### 合成师SurfaceFlinger
SurfaceFlinger是系统的一个服务。前面也一直在提到它专门负责把每个Surface中的内容合成缓存，以待显示到屏幕上。SurfaceFlinger在合成Surface时是根据Surface的Z-order顺序一层一层进行。比如一个Dialog的Surface就会在Activity的Surface上面。然后这个东西不多提了。  

## 终于可以说说你的App为什么这么卡的原因了
通过对Android绘制机制的了解，我们知道造成应用卡顿的根源就在于16ms内不能完成绘制渲染合成过程，因为Android平台的硬件刷新率为60HZ，大概就是16ms刷新一次。如果没能在16ms内完成这个过程，就会使屏幕重复显示上一帧的内容，即造成了卡顿。在这16ms内，需要完成视图树的所有测量、布局、绘制渲染及合成。而我们的优化工作主要就是针对这个过程的。
### 复杂的视图树
如果视图树复杂，会使整个Traversal过程变长。因此，我们在开发过程中要控制视图树的复杂程度。减少不必要的层级嵌套。比如使用RelativeLayout可以减少复杂布局的嵌套。比如使用[**【震惊！这个控件绝对值得收藏。轻松实现圆角、文字描边、状态指示等效果http://www.jianshu.com/p/cfe18cbc6924】**](http://www.jianshu.com/p/cfe18cbc6924)😄，这个控件可以减少既需要显示文字，又需要图片和特殊背景的需求的布局复杂程度，所有的东西由一个控件实现。
### 频繁的requestlayout()
如果频繁的触发requestLayout()，就可能会导致在一帧的周期内，频繁的发生布局计算，这也会导致整个Traversal过程变长。有的ViewGroup类型的控件，比如RelativeLayout，在一帧的周期内会通过两次layout()操作来计算确认子View的位置，这种少量的操作并不会引起能够被注意到的性能问题。但是如果在一帧的周期内频繁的发生layout()计算，就会导致严重的性能，每次计算都是要消耗时间的！而requestLayout()操作，会向ViewRootImpl中一个名为mLayoutRequesters的List集合里添加需要重新Layout的View，这些View将在下一帧中全部重新layout()一遍。通常在一个控件加载之后，如果没什么变化的话，它不会在每次的刷新中都重新layout()一次，因为这是一个费时的计算过程。所以，如果每一帧都有许多View需要进行layout()操作，可想而知你的界面将会卡到爆！卡到爆！需要注意，setLayoutParams()最终也会调用requestLayout()，所以也不能烂用！同学们在写代码的过程中一定要谨慎注意那些可能引起requestLayout()的地方啊！
### UI线程被阻塞
如果UI线程受到阻塞，显而易见的是，我们的Traversal过程也将受阻塞！画面卡顿是妥妥的发生啊。这就是为什么大家一直在强调**不要在UI线程做耗时操作**的原因。通常UI线程的阻塞和以下原因脱不了关系。
- 在UI线程中进行IO读写数据的操作。这是一个很费时的过程好吗？千万别这么干。如果不想获得一个卡到爆的App的话，把IO操作统统放到子线程中去。
- 在UI线程中进行复杂的运算操作。运算本身是一个耗时的操作，当然简单的运算几乎瞬间完成，所以不会让你感受到它在耗时。但是对于十分复杂的运算，对时间的消耗是十分辣眼睛的！如果不想获得一个卡到爆的App的话，把复杂的运算操作放到子线程中去。
- 在UI线程中进行复杂的数据处理。我说的是比如数据的加密、解密、编码等等。这些操作都需要进行复杂运算，特别是在数据比较复杂的时候。如果不想获得一个卡到爆的App的话，把复杂数据的处理工作放到子线程中去。
- 频繁的发生GC，导致UI线程被频繁中断。在Java中，发生GC(垃圾回收)意味着Stop-The-World，就是说其它线程全部会被暂停啊。好可怕！正常的GC导致偶然的画面卡顿是可以接受的，但是频繁发生就让人很蛋疼了！频繁GC的罪魁祸首是**内存抖动**，这个时候就需要看下我的这篇[**【Android内存基础——内存抖动http://www.jianshu.com/p/69e6f894c698】**](http://www.jianshu.com/p/69e6f894c698)文章了。简单的说就是在短时间内频繁的创建大量对象，导致达到GC的阀值，然后GC就发生了。如果不想获得一个卡到爆的App的话，把内存的管理做好，即使这是Java。
- 故意阻塞UI线程。好吧，相信没人会这么干吧。比如sleep()一下？

# 总结

> -  **抽出空余时间写文章分享需要动力，还请各位看官动动小手点个赞，鼓励下喽😄**  
> -  **我一直在不定期的创作新的干货，想要上车只需进到我的个人主页点个关注就好了哦。发车喽～**  

整篇下来，相信童鞋对Android的绘制机制也有了一个比较全面的了解。现在回过头来再写代码时是不是有种知根知底的自信呢？😄  

# 参考链接
1. [Implementing VSYNC：https://source.android.com/devices/graphics/implement-vsync](https://source.android.com/devices/graphics/implement-vsync)
2. [SurfaceFlinger and Hardware Composer：https://source.android.com/devices/graphics/arch-sf-hwc](https://source.android.com/devices/graphics/arch-sf-hwc)
3. [Surface and SurfaceHolder：https://source.android.com/devices/graphics/arch-sh](https://source.android.com/devices/graphics/arch-sh)
4. [Implementing the Hardware Composer HAL：https://source.android.com/devices/graphics/implement-hwc](https://source.android.com/devices/graphics/implement-hwc)
5. [可能是史上最简单的！一张图3分钟让你明白Activity启动流程，不看后悔！http://www.jianshu.com/p/9ecea420eb52](http://www.jianshu.com/p/9ecea420eb52)
6. [惊天秘密！从Thread开始，揭露Android线程通讯的诡计和主线程的阴谋http://www.jianshu.com/p/8862bd2b6a29](http://www.jianshu.com/p/8862bd2b6a29)
7. [震惊！这个控件绝对值得收藏。轻松实现圆角、文字描边、状态指示等效果http://www.jianshu.com/p/cfe18cbc6924](http://www.jianshu.com/p/cfe18cbc6924)
8. [Android内存基础——内存抖动http://www.jianshu.com/p/69e6f894c698](http://www.jianshu.com/p/69e6f894c698)
9. [Android性能优化之渲染篇http://hukai.me/android-performance-render/](http://hukai.me/android-performance-render/)
10. [Android硬件加速原理与实现简介http://tech.meituan.com/hardware-accelerate.html](http://tech.meituan.com/hardware-accelerate.html)
11. [ Android SurfaceFlinger对VSync信号的处理过程分析http://blog.csdn.net/yangwen123/article/details/17001405](http://blog.csdn.net/yangwen123/article/details/17001405)
12. [Android Vsync 原理http://www.10tiao.com/html/431/201601/401709603/1.html](http://www.10tiao.com/html/431/201601/401709603/1.html)
13. [Android Choreographer 源码分析http://www.jianshu.com/p/996bca12eb1d?utm_campaign=hugo&utm_medium=reader_share&utm_content=note](http://www.jianshu.com/p/996bca12eb1d?utm_campaign=hugo&utm_medium=reader_share&utm_content=note)
14. [Android应用程序窗口（Activity）的视图对象（View）的创建过程分析：http://blog.csdn.net/luoshengyang/article/details/8245546](http://blog.csdn.net/luoshengyang/article/details/8245546)
15. [Android 4.4(KitKat)中VSync信号的虚拟化http://blog.csdn.net/jinzhuojun/article/details/17293325](http://blog.csdn.net/jinzhuojun/article/details/17293325)
16. [Understanding necessity of Android VSYNC signals：http://stackoverflow.com/questions/27947848/understanding-necessity-of-android-vsync-signals](http://stackoverflow.com/questions/27947848/understanding-necessity-of-android-vsync-signals)

**看到这里的童鞋快奖励自己一口辣条吧！**
