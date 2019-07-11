![image](http://upload-images.jianshu.io/upload_images/1869462-6225d921c9f3182d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 有什么料？
从这篇文章中你能获得这些料：
- 了解一次触摸事件究竟是如何产生的？
- 了解触摸事件究竟是如何传递的？
- 学会从根源处分析你的App中的滑动冲突。
- 能够更自信的创作出具有复杂交互的App。
- 收获一张图，帮助你理解和使用Android的触摸事件分发。


![link](http://upload-images.jianshu.io/upload_images/1869462-eb8898301c4cb67b.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)    

老规矩，先来看图吧。  

![image](http://upload-images.jianshu.io/upload_images/1869462-a7315cf175c0eb7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)    


# 在你触摸屏幕之后
首先在上图中找到那只黑手，它是一次触摸事件的开始。  

当屏幕被触摸，Linux内核会将硬件产生的触摸事件包装为Event存到` /dev/input/event[x]`目录下。对，你没看错，事件被搞成文件保存了下来！  

接着，系统创建的一个`InputReaderThread`线程**loop**起来让**EventHub**调用`getEvent()`不断的从`/dev/input/`文件夹下读取输入事件。  

然后**InputReader**则从**EventHub**中获得事件交给**InputDispatch**。  

而**InputDispatch**又会把事件分发到需要的地方，比如**ViewRootImpl**的**WindowInputEventReceiver**中。  

以上过程是在底层中完成，大部分由c++实现，我们了解流程就行。  

![image](http://upload-images.jianshu.io/upload_images/1869462-6dae78f55cc336d9.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

下面从**ViewRootImpl**收到触摸事件开始分析触摸事件的去向。  

# 触摸事件的旅行
## 第一站：从InputEventReceiver开始
我们现在图中找到**WindowInputReceiver**。可以看到，它继承了**InputEventReceiver**，并且是**ViewRootImpl**的一个内部类。  

**ViewRootImpl.java**
```
final class WindowInputEventReceiver extends InputEventReceiver｛

  @Override
  public void onInputEvent(InputEvent event) ｛
    ...
    enqueueInputEvent(event, this, 0, true);
    ...
  ｝
｝
```
当一个输入事件产生时（这里我们认为是触摸事件），会回调**InputEventReceiver.onInputEvent()**。从名字也可以看出，它是接收输入事件的。然后进一步调用 **ViewRootImpl.enqueueInputEvent()** 将输入事件加入单链表队列。  

**ViewRootImpl.java**
```
void enqueueInputEvent(InputEvent event,
    InputEventReceiver receiver, 
    int flags, 
    boolean processImmediately) {
    ...
    mPendingInputEventTail = q;
    //进行队列操作后，
    //将ViewRootImpl的mPendingInputEventTail复制为新的触摸事件。
    ...
    if (processImmediately) {
        doProcessInputEvents();
        //立即处理事件
    } else {
        scheduleProcessInputEvents();
        //走一遍Handler延迟处理事件
    }
}
```
上面这个方法主要对触摸事件进行队列操作(即排了个序)，然后再根据`processImmediately`参数，决定是立即处理，还是延后处理。  

下面看看是如何进行处理的。  

```
void doProcessInputEvents() {
    while (mPendingInputEventHead != null) {
        ...
        deliverInputEvent(q);
        //进一步派发事件处理
        ...
    }
}
```
在这个方法中有一个`while{}`循环体。它会将事件队列循环处理，直到队列中没有数据为止。如何处理呢？我们看看`deliverInputEvent()`。  


```
private void deliverInputEvent(QueuedInputEvent q) {
    ...
    InputStage stage;
    if (q.shouldSendToSynthesizer()) {
        stage = mSyntheticInputStage;
    } else {
        stage = q.shouldSkipIme() ? mFirstPostImeInputStage : mFirstInputStage;
    }
    //上面决定将事件派发到那个InputStage中处理
    if (stage != null) {
        stage.deliver(q);
        //派发事件到InputStage中处理
    } else {
        finishInputEvent(q);
    }
}
```
这里说一下有意思的**InputStage**。  
![image](http://upload-images.jianshu.io/upload_images/1869462-906fbb3c4442769a.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

在ViewRootImpl中，有这样一段代码：

```
public void setView(View view, WindowManager.LayoutParams attrs, View panelParentView) {
    ...
    //
    mSyntheticInputStage = new SyntheticInputStage();
    
    InputStage viewPostImeStage = new ViewPostImeInputStage(mSyntheticInputStage);
    
    InputStage nativePostImeStage = new NativePostImeInputStage(viewPostImeStage,
            "aq:native-post-ime:" + counterSuffix);
            
    InputStage earlyPostImeStage = new EarlyPostImeInputStage(nativePostImeStage);
    
    InputStage imeStage = new ImeInputStage(earlyPostImeStage,
            "aq:ime:" + counterSuffix);
            
    InputStage viewPreImeStage = new ViewPreImeInputStage(imeStage);
    
    InputStage nativePreImeStage = new NativePreImeInputStage(viewPreImeStage,
            "aq:native-pre-ime:" + counterSuffix);
    //  
    mFirstInputStage = nativePreImeStage;
    //
    mFirstPostImeInputStage = earlyPostImeStage;
    ...
}
```
如你所见，**InputStage** 是在`setView()`的时候创建的，也就是在**Activity**的`onResume()`阶段，关于这点，你可以看看我这篇文章：[【用两张图告诉你，为什么你的App会卡顿?http://www.jianshu.com/p/df4d5ec779c8】](http://www.jianshu.com/p/df4d5ec779c8)。这些**InputStage**也相当于是单链表结构，一个套一个。也就是说，比如调用了`mFirstPostImeInputStage.deliver(q)`。那么`SyntheticInputStage`, `ViewPostImeInputStage`, `NativePostImeInputStage`, `EarlyPostImeInputStage`都将能够处理这个触摸事件。这里我们主要看看`ViewPostImeInputStage`是如何处理的。  

```
final class ViewPostImeInputStage extends InputStage {

    public final void deliver(QueuedInputEvent q) {
        ...
        apply(q, onProcess(q));
        //onProcess()中处理处理事件
        ...
    
    }

    @Override
    protected int onProcess(QueuedInputEvent q) {
        ...
        return processPointerEvent(q);
        //处理点触摸事件
        ...
    }
    
    private int processPointerEvent(QueuedInputEvent q) {
        final MotionEvent event = (MotionEvent)q.mEvent;
        ...
        final View eventTarget =
            (event.isFromSource(InputDevice.SOURCE_MOUSE) && mCapturingView != null) ?
            mCapturingView :  
            mView;  //DecorView
        ...
        boolean handled = eventTarget.dispatchPointerEvent(event);
        //eventTarget一般取到mView，即DecorView
        ...
        return handled ? FINISH_HANDLED : FORWARD;
    }    
}
```
啊哈，这段代码稍有点长。但是过程很简单。`ViewPostImeInputStage.deliver(q)`调用后，会进一步调用`onProcess()`处理。对于点触摸事件会再进一步调用`processPointerEvent()`处理，并且在这个方法中，通过`eventTarget.dispatchPointerEvent(event)`将触摸事件传递给了**DecorView**的`dispatchPointerEvent()`处理。  

现在越来越清晰了。触摸事件几经辗转终于传递到了View上。赶紧接着看看触摸事件后面的旅途是怎样的？  

## 奇怪的辗转
上面说到触摸事件传递到了`DecorView.dispatchPointerEvent()`中。在图中找到对应位置哦。看看在这个方法中发生了什么？  

找了一会儿可能在**DecorView**中没有找到`dispatchPointerEvent()`方法？哈哈，那一定在更上级。事实上，这个方法是在**View**中实现的。  
**View.java**
```
public final boolean dispatchPointerEvent(MotionEvent event) {
    ...
    return dispatchTouchEvent(event);
    //这个方法大家该熟悉了吧？
    ...
}
```
由于调用的是`DecorView.dispatchPointerEvent()`，所以上面调用的` dispatchTouchEvent(event)`实际是**DecorView**中的。多态！知识点啊！忘了吧？面壁思过吧。  
**DecorView.java**
```
@Override
public boolean dispatchTouchEvent(MotionEvent ev) {
    final Window.Callback cb = mWindow.getCallback();
    //还记得Activity实现了Window.Callback接口吗？
    return cb != null && !mWindow.isDestroyed() && mFeatureId < 0
        ? cb.dispatchTouchEvent(ev) //通常是走这。
        : super.dispatchTouchEvent(ev);
    }
```
在`DecorView.dispatchTouchEvent()`中，又把事件传递到了**Activity**中去了。WHAT？（需要说明一点，**Activity**实现了**Window.Callback**，并且在**PhoneWindow**创建后立即调用`Window.setCallback(this)`，把**Activity**设置为**PhoneWindow**的**Window.Callback**。）好吧，再到**Activity**中看看发生了什么？  
**Activity.java**

```
public boolean dispatchTouchEvent(MotionEvent ev) {
    ... 
    if (getWindow().superDispatchTouchEvent(ev)) {
        //又把事件传到了Window中！
        return true;
    }
    return onTouchEvent(ev); 
    //这就是为什么最后事件没有被消费的话，Activity会去处理的原因。
}
```
如你所见，事件又被传递到了**PhoneWindow**中去了。我的天，不是都到View里了吗？为什么还要一直传递？好吧，继续看应该就知道了。  
![image](http://upload-images.jianshu.io/upload_images/1869462-0eb6e478780f247b.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

**PhoneWindow.java**
```
@Override
public boolean superDispatchTouchEvent(MotionEvent event) {
    return mDecor.superDispatchTouchEvent(event);
}
```
WTF？mDecor分明就是**DecorView**嘛。关于这点，你可以看看我这篇文章：[【用两张图告诉你，为什么你的App会卡顿?http://www.jianshu.com/p/df4d5ec779c8】](http://www.jianshu.com/p/df4d5ec779c8)。为什么来回捣腾一圈又回到**DecorView**中了呢？我们继续看吧。  
**DecorView.java**

```
public boolean superDispatchTouchEvent(MotionEvent event) {
    return super.dispatchTouchEvent(event);
    //千万不要告诉我你不知道这实际会调用ViewGroup的dispatchTouchEvent()方法啊。
}
```
好吧，来回绕啊绕，终于到了各大论坛，各大博客，各大书籍所讲的**触摸事件分发流程**了，即从**ViewGroup**的`dispatchTouchEvent()`开始。相信很多同学早已滚瓜烂熟了，但是下面我还是再撸一遍，以保持整个流程的完整性！  

## 辗转的原因
在开始之前，我们思考一下。既然事件最后要看起来是从**Activity**的`dispatchTouchEvent()`开始往下分发传递，为什么在`InputStage.processPointerEvent()`中不直接把事件传递给**Activity**，而是这样来回绕一圈。这样`DecorView -> Activity -> PhoneWindow -> DecorView`的来回绕一圈不是很折腾吗？看吧，这就是为什么我一直在说你需要看看这篇文章：[【用两张图告诉你，为什么你的App会卡顿?http://www.jianshu.com/p/df4d5ec779c8】](http://www.jianshu.com/p/df4d5ec779c8)的原因。  

首先，为了解耦，**ViewRootImpl**并不知道有**Activity**这种东西存在！不知道！它只是持有了**DecorView**。所以，想要直接把触摸事件送到**Activity.dispatchTouchEvent()** 是不行的。  

那么，既然触摸事件已经到了**Activity.dispatchTouchEvent()**中了，为什么不直接分发给**DecorView** ，而是要通过**PhoneWindow** 来间接发送呢？因为**Activity** 不知道有**DecorView** 这种奇怪的东西存在啊！不知道！但是，**Activity**持有**PhoneWindow** ，而**PhoneWindow**当然知道自己的窗口里有些什么了，所以能够把事件派发给**DecorView** 。你看，在Android中，Activity并不知道自己的Window中有些什么，这样耦合性就很低了。我们换一个Window试试？不管Window里面的内容如何，只要Window任然符合Activity制定的标准，那么它就能在Activity中很好的工作。这就是解耦所带来的扩展性的好处。  

这是我的想法，童鞋们可以对照着图思考思考。

## 最后的事件分发
看图中辣眼睛的蓝色部分。这很熟悉了吧？一般讲触摸事件分发就是从这里开始的。  

我从**DecorView**开始。假设ViewGroup_1是DecorView的子View，ViewGroup_2是ViewGroup_1的子View，View是ViewGroup_2的子View。现在，我要开始描述这个过程了啊，这个地方一定要对照着图撸啊。发车了啊，头手不要伸出窗外。  

1. **DecorView**是**ViewGroup**，所以`dispatchTouchEvent()`【分发器】收到触摸事件后，会询问`onInterceptTouchEvent()`【拦截器】（注意，拦截器是ViewGroup类型的View特有的！）是否需要拦截此次触摸事件？如果拦截返回true，事件将会转到自己的`onTouchEvent()`【处理器】中处理。如果不拦截，事件将传到它的子View，ViewGroup_1的【拦截器】。
2. ViewGroup_1的【分发器】被调用后，会询问自己的【拦截器】是否需要拦截此次触摸事件？如果拦截放回true，事件将会转到自己的【处理器】中处理。如果不拦截，事件将传到它的子View，ViewGroup_2的【拦截器】。
3. ViewGroup_2重复上述步骤。
4. 如果触摸事件到了最后的View中，由于没有【拦截器】，事件将会直接由【分发器】分发到【处理器】中。如果处理了返回true，此次触摸事件的传递到此结束，不会在往下传了。如果不处理，返回false，那么事件将传递到它的父级的【处理器】中。
5. 事实上，不管个层级的【处理器】，如果处理事件返回true，一次触摸事件就结束了。如果不处理事件，返回false，就会传送到上一级的【处理器】中。最终，如果**DecorView**的【处理器】也不打算处理事件，那么事件将会被发送到Activity的【处理器】中处理。

# 总结
> - 抽出空余时间写文章分享需要动力，还请各位看官动动小手点个赞，给我点鼓励吧
> - 我一直在不定期的创作新的干货，想要上车只需进到我的【个人主页】点个关注就好了哦。发车喽～  

本篇从一次触摸事件的产生，讲了它的整个传递过程。对着图撸一撸，然后去教训你的App中的各种**滑动冲突** 、**点击穿透**吧。  

# 这是一个彩蛋
![image](http://upload-images.jianshu.io/upload_images/1869462-77076ad98dba40b6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

很多筒靴可能走了很多路，但任然不知道`onTouchEvent()`和`onTouch()`有何区别？下面将简单的讲一讲。  

首先需要知道`onTouch()`和`onTouchEvent()`都可以接收到触摸事件，但是`onTouch()`的优先级比`onTouchEvent()`更高。也就是说，如果`onTouch()`把事件处理了，那么`onTouchEvent()`就接收不到事件了。  

`onTouch()`是专门被设计用来在View外部对触摸事件进行处理的，你可以通过`View.setOnTouchListener()`来设置一个**OnTouchListener**。

```
mView.setOnTouchListener((v, event) -> {
      LogUtils.e("onTouch");
      return true;
      //返回true才能处理事件。
    });
```
你看，你其实不必为了处理触摸事件专门自定义View，然后重载`onTouchEvent()`的。你可以在View外部使用`View.setOnTouchListener()`来设置一个**OnTouchListener** 处理。  

**需要注意一点，不管`onTouch()`或是`onTouchEvent()`，即使是返回false，你任然可以收到一次ACTION_DWON事件，但是后面的事件就不能继续收到了。**



# 参考链接
1. [用两张图告诉你，为什么你的App会卡顿?http://www.jianshu.com/p/df4d5ec779c8](http://www.jianshu.com/p/df4d5ec779c8)
2. [InputManagerService分析一：IMS的启动与事件传递http://blog.csdn.net/lilian0118/article/details/28617185](http://blog.csdn.net/lilian0118/article/details/28617185)
3. [Android跨进程模拟触屏事件(sendevent)http://azard.me/blog/2015/06/13/android-cross-app-touch-event-injection/](http://azard.me/blog/2015/06/13/android-cross-app-touch-event-injection/)
4. [Android输入事件流程中的EventHub分析及源码演示http://blog.csdn.net/a345017062/article/details/6417929](http://blog.csdn.net/a345017062/article/details/6417929)
5. [Android 4.0 事件输入(Event Input)系统http://blog.csdn.net/myarrow/article/details/7091061](http://blog.csdn.net/myarrow/article/details/7091061)
6. [Correctly implementing onInterceptTouchEvent and onTouchEvent methods for ViewGroup：http://neevek.net/posts/2013/10/13/implementing-onInterceptTouchEvent-and-onTouchEvent-for-ViewGroup.html](http://neevek.net/posts/2013/10/13/implementing-onInterceptTouchEvent-and-onTouchEvent-for-ViewGroup.html)

**看到这里的童鞋快奖励自己一口辣条吧！**
