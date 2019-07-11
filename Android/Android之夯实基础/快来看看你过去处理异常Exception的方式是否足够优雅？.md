![Android-Exception](http://upload-images.jianshu.io/upload_images/1869462-8a985a4446816ec7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 背景介绍
我们每天都需要与各种个样的异常打交到，但是我们对异常了解吗？对其处理方式正确吗？了解的话就算了，不了解的可以看看下面的内容。
# 打开Exception
## Exception的分类
先来看看下面这张图：  
![Exception的分类](http://upload-images.jianshu.io/upload_images/1869462-b88b27381392db6d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
从图中可以看出：
1. Error(错误)和Exception(异常)都继承自Throwable类，我们重点关注Exception；
2. 异常类分为检查异常(直接继承自Exception，除RuntimeException)和可不检查异常(Error和继承自RuntimeException的)；  

## throw和throws的区别
- throw通常在代码片段中用于直接抛出异常。
```
public void test(){
    throw new ClassNotFoundException();
}
```
- throws用在方法签名上，可抛出多种异常
```
public void test() throws IOException, ArrayIndexOutOfBoundsException{
    do something...
}
```  

## 自定义异常
只需要继承Exception就可以了。
```
public class CustomException extends Exception{
    
    public CustomException(String exceptionInfo){
        super(exceptionInfo);
    }
}

//使用CustomExeption
public void test(){
    throw new CustomException("抛出了自定义异常");
}
```
## try-catch-finally
我们都很熟悉使用try-catch-finally去捕获异常，但现在值得思考一下我们之前写的try-catch-finally是否正确了。  
**Code Clean** 指出，try-catch-finally代码块在程序中定义了一个范围，我们应该让它的语意更明确，所以不应该把大段的代码放在其中，而应该抽离出来。来看看下面这个例子。
```
//这个方法中只定义了try-catch，而真正的操作放到deletePagerAndAllReference()中进行
public void delete(){
    try{
        deletePagerAndAllReference();
    } catch (Exception e){
        Log.e(e);
    }
}

public void deletePagerAndAllReference() throws Exception{
    //do delete
}
```
**需要注意，catch中的return语句会在finally执行完成后才会被执行。**

## 关于方法返回null的讨论
在**Code Clean** 中，鲍勃大叔严厉批评了**return null** 这种骇人听闻的做法，这让程序中充满了类似**obj ！= null** 的判断。他建议在可能返回null的地方使用抛出异常，或者直接返回一种特例情况。例如下面这样：
```
List<Employee> employees = getEmployees();
if(employees != null){
    // doSomething...
}
```
由于getEmployees()可能返回null值，所以我们不得不每次调用的时候都去检查是否为null，但如果做如下更改：
```
public List<Employee> getEmployees(){
    if(..there are no employees..){
        return Collections.emptyList();
        //没有数据返回一个空的List，调用时就不必去检查它是否为空了
    }
}
```
或者像下面这样：
```
public List<Employee> getEmployees(){
    if(..there are no employees..){
        throw new NullPointerException("嘿！List<Employee>不能为null,仔细检查下吧！");
        //没有数据返回一个空的List，直接抛出异常，让调用者们知道，这个地方存在错误，不该让List<Employee>为null的。
    }
}
```

## Android中处理未捕获异常，并上报异常信息
在我们的应用中，可能存在一些我们没有捕获的异常，对于这些异常，我们可以把它保存下来，然后进行分析。来看看怎么做。  
首先我们需要**implements Thread.UncaughtExceptionHandler** 实现自己的异常处理类，然后调用** Thread.setDefaultUncaughtExceptionHandler()** 方法把我们的异常处理器设置到系统中，这样有为捕获的异常出现时，就能被我们自己处理了。  
当然，有一个重要的方法需要重写**uncaughtException()** 。下面看看完整例子。

```
public class CrashHandler implements UncaughtExceptionHandler {

  private static final CrashHandler mInstance = new CrashHandler();
  private UncaughtExceptionHandler mDefualtCrashHandler;
  private Context mContext;


  /**
   * 防止被重复创建
   */
  private CrashHandler() {}


  public static CrashHandler getInstance() {
    return mInstance;
  }

  public void init(Context context) {
    mContext = context.getApplicationContext(); // 确保获得的是系统级的Context
    mDefualtCrashHandler = Thread.getDefaultUncaughtExceptionHandler(); // 获取系统默认的异常处理器
    Thread.setDefaultUncaughtExceptionHandler(this); // 把当前实例设置为系统默认异常处理器
  }


  /**
   * 这个方法是我们重写的重点，当系统出现未捕获异常时，就会调用这个方法
   * 
   * @param t 出现未捕获异常的线程
   * @param e 未捕获的异常
   */
  @Override
  public void uncaughtException(Thread t, Throwable e) {
    try {
      saveExceptionToFile(e);
    } catch (Exception ex) {
      ex.printStackTrace();
    }

    if (mDefualtCrashHandler != null) {
      //如果系统有默认异常处理就使用它处理
      mDefualtCrashHandler.uncaughtException(t, e);
    } else {
      //否则我们自行结束程序
      android.os.Process.killProcess(Process.myPid());
    }



  }

  private void saveExceptionToFile(Throwable e) throws IOException{
    if (FileUtils.ExistSDCard()){
      long currentTime = System.currentTimeMillis();
      String crashTime = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(currentTime);
      File file = new File(FileUtils.getAppCrashDir()+"crash" + crashTime + ".txt");
      file.createNewFile();
      try{
        PrintWriter pw = new PrintWriter(new BufferedWriter(new FileWriter(file)));
        pw.println(crashTime);
        printPhoneInfo(pw);
        pw.println();
        e.printStackTrace(pw); //输出错误信息
        pw.close();
      } catch (Exception ex){
        ex.printStackTrace();
      }
    }
  }

  /**
   * 输出手机信息
   */
  private void printPhoneInfo(PrintWriter pw) throws PackageManager.NameNotFoundException {
    PackageManager pm = mContext.getPackageManager();
    PackageInfo pi = pm.getPackageInfo(mContext.getPackageName(), PackageManager.GET_ACTIVITIES);
    pw.print("App version: ");
    pw.print(pi.versionName);
    pw.print("_");
    pw.println(pi.versionCode);

    //android版本号
    pw.print("OS Version: ");
    pw.print(Build.VERSION.RELEASE);
    pw.print("_");
    pw.println(Build.VERSION.SDK_INT);

    //制造商
    pw.print("Vendor: ");
    pw.println(Build.MANUFACTURER);

    //手机型号
    pw.print("Model: ");
    pw.println(Build.MODEL);

    //cpu架构
    pw.print("CPU ABI: ");
    pw.println(Build.CPU_ABI);
  }
}
```
看看怎么使用。
```
public class IceApplication extends MultiDexApplication {
  private static Context context;
  @Override
  public void onCreate() {
    super.onCreate();
    context = this;
    //初始化异常处理类，这样我们的异常类就生效了
    CrashHandler.getInstance().init(context);
  }

  public static Context getAppContext(){
    return context;
  }
}
```
# 总结

现在我们对异常有了一定的了解，从现在开始，在编程过程中要开始注意对异常的处理艺术了。  
  
如果你觉得不错的话，麻烦动动小手点个赞，或者加个关注哦！你的鼓励是我分享的动力。
