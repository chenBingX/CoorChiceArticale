> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

![封面-设计模式.png](http://upload-images.jianshu.io/upload_images/1869462-36511024956bd63d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 定义及实质
- 定义  
提供一个创建对象实例的功能(接口)，而客户调用时无须关心实现过程。属于创建型模式。
- 实质  
选择实现。
# 模式图解
## 简单工厂模式UML图
![单例模式UML图](http://7xrn7f.com1.z0.glb.clouddn.com/16-11-4/61701375.jpg)  
解释：  
从上图可以看出:
1. Factory依赖了Api接口和两个接口的实现类;
2. 而Client依赖Factory和Api接口；
3. ImpA和ImpB是Api接口的不同实现；  
  
Factory工厂类承担了对象实例的创建，而Client只需调用Factory提供的方法(通常是静态方法)就可以获取相应的对象实例，Client完全不清楚对象是如何创建的。  
这种模式实现了用户和对象的解耦，当对象发生变化时，我们无须改动用户的调用，只需在Factory中修改或增加就可以。
```
//对象
public interface Api{
   public void doSomething();
}

public ImpA implements Api{
    @Override
    public void doSomthing(){
        System.out.println("这里是ImpA");
    }
}

public ImpB implements Api{
    @Override
    public void doSomthing(){
        System.out.println("这里是ImpB");
    }
}

//Factory
public class ApiFactory{
    public static Api getApi(int type){
        Api api = null;
        if(type == 0){
            api = new ImpA();
        } else if(type == 1){
            api = new ImpB();
        }
        return api;
    }
}

//Client
public class Client{
    Api api;
    public client(){
        //注意，这里用户并不知道它所获得的对象实例是怎么创建的。
        api = Factory.getApi(0);
        if(api != null){
            api.doSomthing();
        }
    }
}
```
## 简单工厂模式顺序图
![简单工厂模式顺序图](http://upload-images.jianshu.io/upload_images/1869462-d525e343c5501abc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
结合上面代码来看。

# 模式优缺点
## 优势
1. 封装：封装用户创建对象实例的过程。
2. 解耦：使用户不用去关心对象实例的创建过程，并且依赖于抽象。    

## 缺点
用户往往需要知道Factory中选择参数的意义。

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-2ca51ac148384b15.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
