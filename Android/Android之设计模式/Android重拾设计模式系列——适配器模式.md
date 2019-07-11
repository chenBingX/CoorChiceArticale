> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

![封面-设计模式.png](http://upload-images.jianshu.io/upload_images/1869462-36511024956bd63d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 定义及实质
- 定义  
将一个类的接口转换为客户希望的另一个接口，适配器模式使原本由于接口不兼容不能一起工作的类，可以在一起工作。

- 实质  
类型转换，功能复用。  

比较抽象，往下继续看看。

# 模式图解
## 对象适配器
**解释：**  

现在有一个单手剑（现有接口），但英雄只能装备法杖（目标接口）。所以需要定义一个单手剑转换器（适配器），把法杖插进去，它属于单手剑系列，但内部其实是一根法杖（持有现有接口）！当英雄使用单手剑转化器时，实际使用的是法杖。  

### **对象适配器模式UML图**
![对象适配器模式UML图](http://upload-images.jianshu.io/upload_images/1869462-35283b46c7339e23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
从上图可以看出:  
1. **Target接口**为**Client客户端**所要求的接口；
2. **Adapter**是适配器，实现了Target接口，持有**Adaptee**(原始类型，通常它与Client不兼容，但Client又想要使用它的功能)，并且将它适配成了Client要求的Target类型。  

来看看原始类型Adaptee
```
public class Adaptee{
    
    public void specificRequest(){
        //do something...
    }
}
```
Target接口及Adapter接口
```
public interface Target{
    void request();
}

public class Adapter implements Target{
    private Adaptee daptee;
    
    public Adapter{
        daptee = new Adaptee();
    }
    
    public void request(){
        adaptee.sepcificRequest();
    }
}
```

Client如何使用
```
public class Client{
    private Target target;
    
    public static void main(String[] args){
        target = new Adapter(); //创建适配器，但功能仍然是Adaptee的
        target.request(); //实际调用的是Adapter的specificRequest()方法
    }
}
```

### 对象适配器模式顺序图
![对象适配器模式顺序图](http://upload-images.jianshu.io/upload_images/1869462-8059fb12eef276c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
结合上面代码看。  

## 类适配器模式
还是上面那个例子，但这次不需要单手剑转换器了，而是直接创建一种即是单手剑，又是法杖的武器（采用多重继承，但是Java世界不支持！）。在Java宇宙中，只能通过继承单手剑，Copy法杖功能来实现。  
### 类适配器模式UML图
![类适配器模式UML图](http://upload-images.jianshu.io/upload_images/1869462-db26fdea436a4a23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

1. 从图中可以看到，类适配器模式使用了多重继承的特性，但是Java只支持单继承，所以这种模式在Java中不是太适用；
2. Adapter适配器同时继承了Client所期望的类型Target类，和想要使用的功能类Adaptee类。
3. 客户端只需要调用Adapter继承到的specificRequest()方法即可。

这个模式在Java中不常用，而且顺序图比较简单，就不展示了。

## 拓展——接口适配器
严格来说接口适配器并不是标准的适配器模式，它只是借用了适配器的概念而已。接下来看看它是怎么一回事。  

**场景:**  
我们可能遇到过这样一种场景，就是一个interface的方法很多，而我们只想使用其中少数几个，并不想直接实现它，然后把所有方法都重写一遍。  

**解决方法：**  
创建一个abstract抽象类，实现目标接口interface，然后我们就可以继承这个抽象类，选择其中想要的方法去实现逻辑，而不用把所有方法都列出来。  
### 接口适配器模式UML图  
![接口适配器模式UML图](http://upload-images.jianshu.io/upload_images/1869462-8772b2a78a0f5bd4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
如图，AbstractAdapter实现了Api，然后Adapter继承AbstractAdapter就可以选择想要的方法重写就好，而不用把所有方法都列出来。  
它最大的作用其实在写内部类的时候，有的接口要求重写的方法很多，这样会多出很多行无用的代码，这个时候就可以定义一个接口适配器，选择性的列出并重写方法就好。




# 模式优缺点
## 优势
- 转换接口，提高复用性；
- 具有更好的扩展性。我们可以在适配器中扩展新功能，同时还能兼顾老功能。


## 缺点  
- 使用不合理容易造成迷惑。当我们调用A对象的功能时，其内部其实是调用了B的功能，让人不易理解。

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-967abcc3f679e732.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
