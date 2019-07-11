> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

# 介绍
StackTraceElement[]中保存了一条线程中所有调用的方法。其中每个方法的信息包含在一个StackTraceElement对象中。
# 获取StackTraceElement中的信息
```
element.getClassName(); //获取类名
element.getMethodLine(); //获取方法名
element.getMethodName(); //获取方法名
element.getFileName(); //获取包含类的文件名
```
# StackTraceElement[]中的信息
StackTracelElement[]是一个的数组，以下标0为开始，其中在Java比较有用的是第3个，**在Android中比较有用的是第4个**,因为在Android中多了一个Dalvik的调用，所以实际Java中的第3位向后移动了一个位置，变成第4位。   
**这个比较有用的StackTraceElement保存了实际调用该方法的方法信息。**


```
//下面方法能够打印一组当前线程的StackTraceElement[]信息：
public static String showAllElementsInfo() {
    String print = "";
    int count = 0;
    //从当前线程中获取所有的StackTraceElement
    StackTraceElement[] stackTraceElements = Thread.currentThread().getStackTrace();
    for (StackTraceElement stackTraceElement : stackTraceElements) {
      count++;
      print += String.format("ClassName:%s " +
          "\nMethodName:%s " +
          "\nMethodLine:%d " +
          "\n当前是第%d个 " +
          "\n---------------------------- " +
          "\n ",
          stackTraceElement.getClassName(),
          stackTraceElement.getMethodName(),
          stackTraceElement.getLineNumber(),
          count);
    }
    return print;
  }
  
  //我是在单元测试中调用的
  @Test
  public void testTestElement() throws Exception {
    System.out.println(LogUtils.showAllElementsInfo());
  }


//下面是测试打印结果：
ClassName:java.lang.Thread 
MethodName:getStackTrace 
MethodLine:1552 
当前是第1个 
---------------------------- 
 ClassName:com.example.chenbing.animdvedemo.Utils.LogUtils 
MethodName:showAllElementsInfo 
MethodLine:60 
当前是第2个 
---------------------------- 
 ClassName:com.example.chenbing.animdvedemo.Utils.LogUtilsTest 
MethodName:testTestElement 
MethodLine:32 
当前是第3个 
---------------------------- 
 ClassName:sun.reflect.NativeMethodAccessorImpl 
MethodName:invoke0 
MethodLine:-2 
当前是第4个 
---------------------------- 
 ClassName:sun.reflect.NativeMethodAccessorImpl 
MethodName:invoke 
MethodLine:62 
当前是第5个 
---------------------------- 
 ClassName:sun.reflect.DelegatingMethodAccessorImpl 
MethodName:invoke 
MethodLine:43 
当前是第6个 
---------------------------- 
 ClassName:java.lang.reflect.Method 
MethodName:invoke 
MethodLine:498 
当前是第7个 
---------------------------- 
 ClassName:org.junit.runners.model.FrameworkMethod$1 
MethodName:runReflectiveCall 
MethodLine:50 
当前是第8个 
---------------------------- 
 ClassName:org.junit.internal.runners.model.ReflectiveCallable 
MethodName:run 
MethodLine:12 
当前是第9个 
---------------------------- 
 ClassName:org.junit.runners.model.FrameworkMethod 
MethodName:invokeExplosively 
MethodLine:47 
当前是第10个 
---------------------------- 
 ClassName:org.junit.internal.runners.statements.InvokeMethod 
MethodName:evaluate 
MethodLine:17 
当前是第11个 
---------------------------- 
 ClassName:org.junit.internal.runners.statements.RunBefores 
MethodName:evaluate 
MethodLine:26 
当前是第12个 
---------------------------- 
 ClassName:org.junit.internal.runners.statements.RunAfters 
MethodName:evaluate 
MethodLine:27 
当前是第13个 
---------------------------- 
 ClassName:org.junit.runners.ParentRunner 
MethodName:runLeaf 
MethodLine:325 
当前是第14个 
---------------------------- 
 ClassName:org.junit.runners.BlockJUnit4ClassRunner 
MethodName:runChild 
MethodLine:78 
当前是第15个 
---------------------------- 
 ClassName:org.junit.runners.BlockJUnit4ClassRunner 
MethodName:runChild 
MethodLine:57 
当前是第16个 
---------------------------- 
 ClassName:org.junit.runners.ParentRunner$3 
MethodName:run 
MethodLine:290 
当前是第17个 
---------------------------- 
 ClassName:org.junit.runners.ParentRunner$1 
MethodName:schedule 
MethodLine:71 
当前是第18个 
---------------------------- 
 ClassName:org.junit.runners.ParentRunner 
MethodName:runChildren 
MethodLine:288 
当前是第19个 
---------------------------- 
 ClassName:org.junit.runners.ParentRunner 
MethodName:access$000 
MethodLine:58 
当前是第20个 
---------------------------- 
 ClassName:org.junit.runners.ParentRunner$2 
MethodName:evaluate 
MethodLine:268 
当前是第21个 
---------------------------- 
 ClassName:org.junit.runners.ParentRunner 
MethodName:run 
MethodLine:363 
当前是第22个 
---------------------------- 
 ClassName:org.junit.runner.JUnitCore 
MethodName:run 
MethodLine:137 
当前是第23个 
---------------------------- 
 ClassName:com.intellij.junit4.JUnit4IdeaTestRunner 
MethodName:startRunnerWithArgs 
MethodLine:69 
当前是第24个 
---------------------------- 
 ClassName:com.intellij.rt.execution.junit.JUnitStarter 
MethodName:prepareStreamsAndStart 
MethodLine:234 
当前是第25个 
---------------------------- 
 ClassName:com.intellij.rt.execution.junit.JUnitStarter 
MethodName:main 
MethodLine:74 
当前是第26个 
---------------------------- 
 ClassName:sun.reflect.NativeMethodAccessorImpl 
MethodName:invoke0 
MethodLine:-2 
当前是第27个 
---------------------------- 
 ClassName:sun.reflect.NativeMethodAccessorImpl 
MethodName:invoke 
MethodLine:62 
当前是第28个 
---------------------------- 
 ClassName:sun.reflect.DelegatingMethodAccessorImpl 
MethodName:invoke 
MethodLine:43 
当前是第29个 
---------------------------- 
 ClassName:java.lang.reflect.Method 
MethodName:invoke 
MethodLine:498 
当前是第30个 
---------------------------- 
 ClassName:com.intellij.rt.execution.application.AppMain 
MethodName:main 
MethodLine:144 
当前是第31个 
---------------------------- 
```

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-4b9bea130830c939.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
