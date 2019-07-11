> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

![封面-设计模式.png](http://upload-images.jianshu.io/upload_images/1869462-36511024956bd63d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 定义及实质
- **定义**  

为子系统中的一组接口提供一个一致的界面，这个界面使得子系统更加容易使用。  

**解释**：英雄吹响号角（相当于统一界面，用于发动攻击），命令部队对敌军发动攻击；接下来部队里的各种兵种开始对敌军发动攻击。咕噜兵发起了攻击，狼骑发起了攻击，牛头人发起了攻击，风骑士发起了攻击...号角这个统一界面，把部队发起攻击的事件进行了封装，使得英雄不用去了解发起攻击时，部队中的每个兵种是如何发起攻击的。  


- **实质**  

封装交互，简化调用。  

# 模式图解
## 外观模式UML图
![外观模式UML图](http://upload-images.jianshu.io/upload_images/1869462-4e74133967c1c771.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
从上图可以看出:  
1. Facade作为外观类，它提供一个**test()** 方法入口，供CLient客户端调用子系统功能；
2. 可以看到Facade依赖了子系统中所有类，因为它要负责调度它们；Facade其实就是把子系统的功能封装起来，这样使得Client仅仅通过一个**test()** 方法就能使用子系统。  
来看看代码：  
功能模块的代码:  

```
//A功能模块
public interface AMoudleApi{
    void testA();
}

public class AMoudle implements AMoudleApi{
    public void testA(){
        System.out.println("AMoudle");
    }
}

//B功能模块
public interface BMoudleApi{
    void testB();
}

public class BMoudle implements BMoudleApi{
    public void testB(){
        System.out.println("BMoudle");
    }
}

//A功能模块
public interface CMoudleApi{
    void testC();
}

public class CMoudle implements CMoudleApi{
    public void testC(){
        System.out.println("CMoudle");
    }
}
```
Facade外观类：
```
public class Facade{
    private AMoudleApi aMoudle;
    private BMoudleApi bMoudle;
    private CMoudleApi cMoudle;
    public Facade{
        aMoudle = new AMoudle();
        bMoudle = new BMoudle();
        cMoudle = new CMoudle();
    }
    
    public void test(){
        aMoudle.testA();
        bMoudle.testB();
        cMoudle.testC();
    }
}
```
下面看看Client客户端是如何使用的：
```
public class Client{
    private Facade facade;
    public static void main(String[] args){
        facade = new Facade()
        facade.test(); //调用Facade的test()方法就能调用到上面所有功能模块，而不用依赖它们
    }
}
```


## 外观模式顺序图
![外观模式顺序图](http://upload-images.jianshu.io/upload_images/1869462-2b6b24329dc705e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
结合上面的代码看这个过程图。



# 模式优缺点
## 优势
1. 松散耦合，使客户端和子系统解耦，似的扩展更容易进行；
2. 简单易用；
3. 更好的划分访问层次。


  
  
## 缺点
1. 使用不合理容易让人不明白是调用Facade接口好，还是直接调用子系统功能接口好。

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-a9b169b0a3ed8ac7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
