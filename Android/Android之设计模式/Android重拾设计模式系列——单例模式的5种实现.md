> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

![封面-设计模式.png](http://upload-images.jianshu.io/upload_images/1869462-36511024956bd63d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

单例模式是我们最常使用，也是最简单的一种模式，主要用于只想系统中存在一个实例的情况，比如某个Manager。

# 定义及实质
- 定义  
确保某一个类只有一个实例，而且自行实例化并向系统提供这个实例。  

- 实质  
控制实例数量，确保只有一个实例。

# 模式图解
## 单例模式UML图
![单例模式UML图](http://upload-images.jianshu.io/upload_images/1869462-7a85fa5ab371de40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
很直观明了，很简单。下面来看看单例模式的不同实现方案。  

### 饿汉式
```
public class Singleton{
    private static fianl Singleton instance = new Singleton();
    
    //私有化构造器，避免外部访问。使用反射仍然可以访问，所以安全是相对的。
    //但仍然可以通过哈希值等进行限制，提高安全性。
    priavte Singleton(){
    }
    
    //对外暴露的接口，用于获取实例
    public static Singleton getInstance(){
        return instance;
    }
    
    public void doSomething(){
        System.out.println("doSomething");
    }
}
```
**解释：**   
1. 饿汉式是利用了**static** 关键字在类加载时就会进行初始化，并且缓存到**静态内存** 中的特点，确保了调用**getInstance()** 时，无须担心**instance** 为null；
2. 通过**fianl** 关键字，式单例在多线程情况下的安全，因为JVM会自动对**fianl** 进行上锁同步。  

**优点：** 能够在线程安全的情况下实现单例。  
**缺点：** 由于类一加载就会创建实例，所以会较早占用系统资源。
  
### 懒汉式
```
public class Singleton{
    private static Singleton instance;

    priavte Singleton(){
    }
    
    //加synchronized上锁，可以一定程度上确保安全性
    public static synchronized Singleton getInstance(){
        if(instance == null){
            instance = new Singleton();
        }
        return instance;
    }
    
    public void doSomething(){
        System.out.println("doSomething");
    }
}
```
**解释：**   
1. 懒汉式体现了延迟加载的的思想。对象实例只有在第一次调用**getInstance()** 方法时才会被创建，一定程度上的节约了系统资源；
2. 懒汉式在单线程下能够很好的工作，但是并发下就很有可能会创建多个实例。  

**优点：** 能够实现延迟加载，节约内存。在单线程中能很好工作。  
**缺点：** 并发下可能会创建多个实例，每次判断都会耗费一些时间。

### DCL双重检查实现单例
```
public class Singleton{
    //这里使用了volatile关键字，它能够确保insatnce变量每次都直接从主内存(而不是寄存器)中加载最新赋值。
    private volatile static Singleton instance = null;

    priavte Singleton(){
    }
    
    //这里进行了两次null检查，即双重检查锁定，这能很大程度的确保安全性
    public static Singleton getInstance(){
        if(instance == null){
            synchroniazed(Singleton.class){
                if(instance == null){
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
    
    public void doSomething(){
        System.out.println("doSomething");
    }
}
```

**优点：** 既能很大程度上确保线程安全，又能实现延迟加载。  
**缺点：** 使用**volatile** 关键字会使JVM对该段代码的优化丧失，影响性能。并且在一些高并发的情况下，仍然可能创建多个实例，这称为**双重检查锁定失效** ，有一些书中作者均认为这是一种“丑陋”的单例实现方案。  

### 静态内部类实现单例
```
public class Singleton{
    priavte Singleton(){
    }
    
    public static Singleton getInstance(){
        return SingletonHolder.instance;
    }
    
    //静态内部类确保了在首次调用getInstance()的时候才会初始化SingletonHolder，从而导致实例被创建。
    //并且由JVM保证了线程的安全。
    priavte static class SingletonHolder{
        priavte static final Singleton instance = new Singleton();
    }
    
    public void doSomething(){
        System.out.println("doSomething");
    }
}
```  

这是单例模式最好的实现方法之一。  

### 枚举类实现单例
枚举能够确保实例的唯一性，能够最大程度上确保线程安全，并且提供无偿序列化机制。所以在不对延迟加载有太高要求的情况下，使用枚举创建单例是最佳的方案！
```
public enum Singleton{
    INSTANCE;
    
    public void doSomething(){
        System.out.println("doSomething");
    }
}
```

# 拓展
## 以下几种情况下JVM会自动帮助我们完成同步：
- 静态初始化器(static{}代码块)初始化数据时；
- 访问final字段时；
- 在创建线程之前创建对象；
- 线程可以看见它将要创建的对象时。

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-7741d1477c443473.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
