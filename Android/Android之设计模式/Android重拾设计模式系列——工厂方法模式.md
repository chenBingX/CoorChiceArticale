> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

![封面-设计模式.png](http://upload-images.jianshu.io/upload_images/1869462-36511024956bd63d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

工厂方法模式和简单工厂模式本质是类似的，都是用来封装产品的创建过程。但它们的实现也有很大的，所以通常被作为两种不同的设计模式看待。  
  
  
[简单工厂模式传送门](http://www.jianshu.com/p/a4b4035842fe)
# 定义及实质
- **定义**  
定义一个用于创建对象的接口，让子类决定实例化那一个对象，即使一个类的实现延迟到子类实现。
- **实质**  
延迟到子类选择实现。  
  
**虽然都是选择实现，但工厂方法模式主要用于创建一系列的产品簇(就是相似的东西)，而简单工厂法可以创建任何互相没有关联的实例。** 
# 模式图解
## 工厂方法模式UML图
![工厂方法模式UML图](http://upload-images.jianshu.io/upload_images/1869462-10f472a00ac714e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
从上图可以看出：
1. Creator是抽象父类，ConcreteCreator是其子类；
2. ConcreteCreator重写了父类的**factoryMethod()** 来创建真正的产品ConcreteProduct，也就是说ConcreteCreator依赖ConcreteProduct；需要注意，Creator依赖的是Product；
3. ConcreteProduct实现了Product接口。  

```
//Creator
public abstract class Creator{
    public abstrat Product factoryMethod();
    
    public void someOpration(){
        //在这里延迟创建了Product
        factorMethod().doSomthing();
    }
}

//ConcreteCreator
public class ConcreteCreator extends Creator{
    @Overrid
    public Product factoryMethod(){
        return new ConcreteProduct();
    }
}

//Product
public interface Product{
   void doSomthing();
}

//public class ConcreteProduct implement Product{
    @Overrid 
    public void doSomthing(){
        System.out.println("ConcreteProduct");
    }
}
```
下面看看Client怎么使用:
```
public class Client{
    priavte Creator creator;
    public static void main(String[] args){
        creator = new ConcreteCreator(); //创建符合需求的Creator子类
        creator.someOpration(); //调用这个方法才会创建Product
    }
}
```

## 工厂方法模式顺序图
![工厂方法模式顺序图](http://upload-images.jianshu.io/upload_images/1869462-a3dd698a1e9f806d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
结合上面的代码看。

# 模式优缺点
## 优势
- 体现了**依赖倒置** 的原则，使程序的可扩展性增强，当有新需求时，只需要增加新的Creator子类，替换Client中的子类，而不需要修改其它地方；
- 可以在不知道对象创建过程未确定的情况下进行编程；
- 可以很好的链接平行类层次结构。  

## 缺点
- 工厂类和产品类耦合在一起。  

# 拓展
## 平行类层次结构
指一个类层级(工厂方法模式中，每个Creator的抽象就代表着一个类层级)中的每一个类，在另一个类层级中都有对应的类与之呼应，这样的一组类层就称为**平行类层次结构** 。  
**解释：**   
以Shape为例，具体的Shape对象和Shape对象的行为就构成了平行类层次结构。Shape对象层级中有Rectangle、Oval等，在Shape对象行为层级中，有Rectangle、Oval等的面积、周长等行为与之对应。所以它们构成了平行类层次结构。

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-54767b38a7912c9d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
