> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

![封面-设计模式.png](http://upload-images.jianshu.io/upload_images/1869462-36511024956bd63d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

抽象工厂模式和工厂方法模式很相似，**都是延迟子类选择创建**，但它们的不同点在于：  

工厂方法模式专注于创建单个完整的产品，而抽象工厂模式专注于创建构一个完整产品的所有部件。比如说，工厂方法模式创建一把单手剑，而抽象工厂模式需要创建剑柄、剑体、需要用的材料等。它们思想相同，但关注点不一样。工厂方法模式可以理解为宏观，而抽象工厂模式可以理解为微观。  
[[工厂方法模式传送门]](http://blog.csdn.net/qq_31370269/article/details/53162291)  

# 定义及实质
- **定义**  

提供创建一系列相关或相互依赖的对象的接口，而无需指定它们的具体类。 

- **实质**  

选择产品簇的实现。  

# 模式图解
## 抽象工厂模式UML图
![抽象工厂模式UML图](http://upload-images.jianshu.io/upload_images/1869462-eda73711a781a5da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
从上图可以看出:  
1. 首先定义了一个抽象工厂类**AbstractFactory**，它相当于一个模版，它能够生产具有相关关系的产品A、B，其子类工厂按照这个规范进行扩展；
2. 定义两种产品接口(产品模版)，**AbstarctProductA**和**AbstarctProductB**，它们是相关的产品类型，比如说剑柄和剑体；
3. 继承抽象工厂类**AbstractFactory**，定义两种不同的工厂，分别用于生产1系列产品和2系列产品。  
下面是工厂类：

```
//抽象工厂类AbstractFactory
public abstract class AbstractFactory{
    public abstract AbstractProductA createProductA();
    public abstract AbstractProductB createProductB();
}

//1系列产品工厂
public class ConcreteFactory1{
    @Overrid
    public AbstractProductA createProductA(){
        return new ProductA1();
    }
    
    @Overrid
    public AbstractProductB createProductB(){
        return new ProductB1();
    }
}

//2系列产品工厂
public class ConcreteFactory1{
    @Overrid
    public AbstractProductA createProductA(){
        return new ProductA2();
    }
    
    @Overrid
    public AbstractProductB createProductB(){
        return new ProductB2();
    }
}
```
下面是产品类：  

```
//A类产品接口(模版)
public interface AbstractProductA{
    void doSomthing();
}

//A1产品
public class ProductA1 implement AbstractProductA{
    @Overrid
    public void doSomething(){
        System.out.println("ProductA1");
    }
}

//A2产品
public class ProductA2 implement AbstractProductA{
    @Overrid
    public void doSomething(){
        System.out.println("ProductA2");
    }
}

//B类产品接口(模版)
public interface AbstractProductB{
    void doSomthing();
}

//B1产品
public class ProductB1 implement AbstractProductB{
    @Overrid
    public void doSomething(){
        System.out.println("ProductB1");
    }
}

//B2产品
public class ProductB2 implement AbstractProductB{
    @Overrid
    public void doSomething(){
        System.out.println("ProductB2");
    }
}
```
下面看看Client客户端如何使用：  

```
public class Client{
    public static void main(String[] args){
        //现在需要创建1系列产品
        AbstractFactory factory = new ConcreteFactory1();
        ProductA a1 = factory.createProductA();
        ProductB b1 = factory.createProductB();
        
        //接下来创建2系列产品
        factory = new ConcreteFactory2();
        ProductA a2 = factory.createProductA();
        ProductB b2 = factory.createProductB();
    }
}
```

## 抽象工厂模式顺序图
![抽象工厂模式顺序图](http://upload-images.jianshu.io/upload_images/1869462-30a048948cc4f56e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
结合上面代码来看顺序图。

# 模式优缺点
## 优势
1. 封装复杂创建逻辑；
2. 解耦Client和产品；
3. 方便切换产品簇，只需更换工厂实例就行。

## 缺点
1. 不容易扩展新产品，当需要增加新的产品类型时，不得不把之前的每个工厂类都修改；
2. 容易造成选择层次的复杂，因为当产品类型多时，配置方案也将增加。

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-0f6e3b21928d2caa.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
