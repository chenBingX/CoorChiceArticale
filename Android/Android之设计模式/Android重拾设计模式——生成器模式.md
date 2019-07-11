> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

![封面-设计模式.png](http://upload-images.jianshu.io/upload_images/1869462-36511024956bd63d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 定义及实质
- 定义  
将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。


- 实质  
分离整体构建算法和部件构造。  

**解释**:   
打魔兽的时候，玩家就相当于Director指导者，兵营就相当于Builder生成器，而Product产品就是各种兵。玩家指导兵营生产不同的兵种，玩家可以通过自己的一些思考(构建算法)决定什么兵生产几个，开始或终止生产，而兵营只负责去构建这些兵种，并且对玩家暴露驱动生产的接口。


# 模式图解
## 生成器模式UML图
![生成器模式UML图](http://upload-images.jianshu.io/upload_images/1869462-2a4bc50d510e6520.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
从上图可以看出:  
1. Director指导者，它拥有Builder构建者，负责实现构建算法，在适合的时候调用Builder创建部件或产品，并且具备配置Builder参数的能力，以便能够动态的创建符合要求的部件或产品；
2. Builder构建者能够根据参数实现具体固定的构建过程，ConcreteBuilder实现了一种具体的构建过程；
3. Product产品接口（不是必要的，在简化的生成器模式中Builder作为静态内部类存在于Product中）。  

Talk is cheap, show me the Code.  

```
//Director
public class Director{
    private Builder builder;
    
    //Director指导者需要持有Builder构建者
    public Director(Builder builder){
        this.builder = builder;
    }
    
    public void construct(){
        builder.build();
    }
}

//Builder
public interface Builder{
    void build();
}

//ConcreteBuilder
public class ConcreteBuilder implements Builder{
    private Product resultProduct;
    
    private String property;
    
    public ConcreteBuilder(){
    
    }
    
    public Product build(){
        this.resultProduct = new ProductA();
        resultProduct.setProperty(property);
        return resultProduct;
    }
    
    public Product setProperty(String property){
        this.property = property;
    }
    
    public Product getResult(){
        return resultProduct;
    }
}

//Product接口
public interface Product{
    
}

public class ProductA{
    private String property;
    
    public void setProperty(String property){
        this.property = property;
    }
}
```  

看看Client客户端如何使用：  

```
public class Client{
    
    publuc static void main(Stringp[] args){
    Product product;
    Builder builder = new ConcreteBuilder(); //创建生成器
    buidler.setProperty("property");
    Director director = new Director(builder); //创建指导者
    director.construct(); //构建产品
    product = builder.getResult(); //获得产品实例
    }
}
```

- 构建器模式可以省略Director，直接把Builder作为静态内部类嵌入到产品类中，然后把构建算法放到Client中。  

```
public class Product{
    private String property1;
    private String property2;
    private String property3;
    
    private Product(){
        
    }
    
    private void setProperty1(String property1){
    //可在这里对参数进行检查，如：
        if(property1.equals("condition1")){
            System.out.println("property1 is error.")
        }
        
        this.property1 = property1;
    }
    
    private void setProperty2(String property2){
        this.property2 = property2;
    }
    
    private void setProperty3(String property3){
        this.property3 = property3;
    }
    
    public static class Builder{
        private String property1;
        private String property2;
        private String property3;
        
        public Builder setProperty1(String property1){
        this.property1 = property1;
        return this;
        }
    
        public Builder setProperty2(String property2){
        this.property2 = property2;
        return this;
        }
    
        public Builder setProperty3(String property3){
        this.property3 = property3;
        return this;
        }
        
        public Buidler build(){ //配置好属性之后，最终调用这个方法来创建对象
        
        //也可在这里对参数进行检查，如：
        if(property1.equals("condition1")){
            System.out.println("property1 is error.")
        }
        
        if(property2.equals("condition2")){
            System.out.println("property2 is error.")
        }
        
        if(property3.equals("condition3")){
            System.out.println("property3 is error.")
        }
        
        //创建Product
        Product product = new Product();
        product.setProperty1(property1);
        product.setProperty2(property2);
        product.setProperty2(property2);
        
        return product;
        }
        
        
    }
    
}
```

## 生成器模式顺序图
![生成器模式顺序图](http://upload-images.jianshu.io/upload_images/1869462-44c4166d4ee7dbda.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

结合上面代码来看这个图。

# 模式优缺点
## 优势
  
- 松散耦合：将构建算法和构建过程分离，同时也与客户端隔离；
- 方便修改：由于松散耦合，依赖抽象，所以修改很容易进行；
- 复用性更好。

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-7e98f4cadb3c0625.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
