![图片发自简书App](http://upload-images.jianshu.io/upload_images/1869462-4ed880f2b570c43e.jpg)

想要了解更多的我？请进入我的主页查看关于我的更多详细资料。

# 参考资料
[参考资料](https://developer.android.com/reference/java/lang/ClassLoader.html);  
[包括热修复](http://kymjs.com/code/2016/05/08/01);
# 背景介绍
Java程序是由一个个.class文件组成的，在程序执行阶段，JVM采用了动态加载的策略，当一个类被使用时，将由一个ClassLoad实例去加载这个.class文件。Java允许我们从外部加载一个类到内存中，然后使用它。
# 有关ClassLoader
## Java中ClassLoader的分类
	(1) BootstarpClassLoad：启动类加载器，它负责在程序启动时去加载Java的核心库。
	(2) ExtensioClassLoad：扩展类加载器，它负责加载扩展库。
    (3) AppClassLoad：系统类加载器，它负责加载classpath下的.class文件。
    (4) CustomClassLoader：自定义的ClassLoader
## ClassLoader的加载机制
ClassLoader采用了**双亲委托加载机制** ，先看看下面的图解。  
![ClassLoader加载机制](http://upload-images.jianshu.io/upload_images/1869462-35aaf6cb39d3b330.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
1. 可以看到，ClassLoader在加载一个类时，会先自下而上的检查目标时候被加载了；
2. 然后，自上而下的依次尝试去加载目标，如果到最后一层ClassLoader仍然没有加载到，就会抛出**ClassNotFoundException** 错误。
3. 注意图中每种类型ClassLoader负责的范围。  

## 开始自定义ClassLoader
### 认识关键方法
- findClass(String name)：这个方法顾名思义负责查找一个类，并返回它。对我们自定义而言，这是我们最需要关注的，一般情况下，我们只需要直接在这个方法中返回目标类就可以了，这也是Google推荐我们的做法。
- loadClass(String name)：这个方法中主要负责协调加载类，通常它的逻辑比较固定，我们可以不去重写。在这个方法中，先尝试通过父类ClassLoader去加载目标类，没有加载到，然后调用findClass()方法去查找。
- defineClass(String name, byte[] b, int off, int len)：负责定义类，这个方法我们主要调用就好了。  

### 看看例子
```
public class CustomClassLoader extends ClassLoader {

  private String classPath;

  public CustomClassLoader(String classPath) {
    super(CustomClassLoader.class.getClassLoader());
    this.classPath = classPath;
  }

  @Override
  public Class<?> findClass(String name) throws ClassNotFoundException {
     //检查路径是否可用
    if (classPath == null || classPath.equals("")) {
      throw new IllegalArgumentException("Please set class path first.");
    }
    //加载class文件数据
    byte[] classData = loadClassData(classPath);
    if (classData == null) {
      throw new NullPointerException(
          "Try to get the byte[] that read from class file, but mate some problem. Please check class file path.");
    }
    return defineClass(name, classData, 0, classData.length); // 将class的字节数组解码为Class实例
  }

  /**
   * 读取Class文件。这就是一个读文件的操作嘛！
   */
  private byte[] loadClassData(String path) {
    byte[] bytes = new byte[1024];
    int length = 0;
    ByteArrayOutputStream baos = new ByteArrayOutputStream();
    File classFile = new File(path);
    FileInputStream fis = null;
    try {
      fis = new FileInputStream(classFile);
      while ((length = fis.read(bytes)) != -1) {
        baos.write(bytes, 0, length);
        baos.flush();
      }
      return baos.toByteArray();
    } catch (Exception e) {
      e.printStackTrace();
    } finally {
      try {
        if (fis == null) {
          throw new NullPointerException(
              "Can not create FileInputStream, please check the file path.");
        }
        fis.close();
      } catch (IOException e) {
        e.printStackTrace();
      }
    }
    return null;
  }

  public void setClassPath(String classPath) {
    this.classPath = classPath;
  }

}

//下面来看看怎么使用
public class MyClass {
  public static void main(String[] args){
    CustomClassLoader classLoader = new CustomClassLoader("");
    try {
      classLoader.setClassPath("/Users/.../TestClass.class");
      Class clazz = classLoader.loadClass("TestClass");
      //通过反射来调用他的方法
      Method method = ReflectUtils.getMethod(clazz,"doSomething");
      System.out.println(clazz.getSimpleName());
      System.out.println("result = " + method.invoke(clazz.newInstance()));
    } catch (Exception e) {
      e.printStackTrace();
    }
  }
}

//下面是TestClass
public class TestClass {
  public int doSomething(){
    return 998;
  }
}

//下面是输出结果
TestClass
result = 998

```
### JVM如何判断两个类是否相同
1. 是否具有相同类名；
2. 是否由同一个ClassLoader加载。

# 总结
其实Java已经做了很多工作，我们自定义ClassLoader要做的事就一件：**读取.class文件** 。其余的通常不需要修改。  

想要了解更多的我？请进入我的主页查看关于我的更多详细资料。

作者利用空余时间分享知识不容易，看完给个赞，加个关注吧。如果你要请我吃辣条，请用打赏砸我吧🤑
