> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

# 什么是注解依赖
- 依赖：当A类中有B类的实例时（或者A类需要用到B类的一些特性），就称B类为A类的依赖。
- 注解：无需new就能把B类的一个实例添加到A类中的操作。
# Dagger2简介
Dagger2是由Google接手Dagger1开发的一个注解框架，它的主要特性就是利用**有向无环**（这种结构意味着在你设计注解框架的时候，不能形成循环）这样一种结构来描述类间的关系，从而达到一种“描述创建”的效果。比如：现在需要一台 **咖啡机** ，可以通过事先描述好其如何制造（相当于描述了一个蓝图），然后你就只需要说（即使用 **@Inject** 注解符）来一台咖啡机，Dagger就会制造一台在你指定的地方。
# 注解符
## @Inject
- 用于告诉Dagger，你现在需要注入一个实例到这个地方。
```
public class CoffeMaker｛
    @Inject
    CoffeMachine coffeMachine;
    /*Dagger会自动识别本次需求，并开始匹配CoffeMachine
    的制造说明，制造一台CoffeMachine放倒该类中。
    */
```
- 使用@Inject注解构造器，可以告诉Dagger，该构造器当有地方需要注入实例时，可以使用这个构造器来创建实例。

```
public class CoffeMachine{
    //现在Dagger已经知道了这个构造器的存在，在需要时将被调用。
    @Inject
    public CoffeMachine(){
        initCoffeMachine();
    }
}

```
## @Module
用于标识一个模型类，实例的创建描述需要放到这个类中，并且每一个创建规则都需要用 **@Provides** 来标注，这样Dagger才能在需要调用的时候去匹配它。
```
@Module
public class CoffeMachineModule｛
    private CoffeMachine mCoffeMachine;
    public CoffeMachineModule(CoffeMachine coffeMachine)｛
        mCoffeMachine = coffeMachine
    }
    
    /**
    * 当有地方调用了@Inject CoffeMachine coffeMachine
    * 之后，Dagger就会匹配该方法来提供一个实例给调用者。
    */
    @Provides
    CoffeMachine providesCoffeMachine(){
        return mCoffeMachine;
    }
```
## @Provides
必须放在@Module类中，或者说有这个注解符的类都需要使用@Module注解。这里需要注意的是，@Provides注解的方法中如果有参数的话，那么这个参数也必须由一个@Provides所注解的方法提供。它相当于标注了一个实例提供者。
```
/*这个实例提供者需要一个Heater参数，这个参数又需要下面的
  Heater实例提供者来提供。以此类推。
*/
@Provides
CoffeMachine2 providesCoffeMachine2(Heater heater){
    return new CoffeMachine2(heater);
}

@Provides
Heater provideHeater(){
    return new Heater();
}
```
## @Component
这是个组件使@Inject与@Module产生关联的接口类，并且Dagger会根据这个类自动生成一个有名为**Dagger + 该接口类类名** 的类，这个类采用的是构造器（Builder）模式。在这个接口中，可以指明需要注解的地方，或者暴露一些所需的实例。
```
@Component(module = {CoffeMachine.class})
public interface CoffeMakerComponent{
    //这个方法指明需要注解的地方
    TagerActivity inject(TagerActivity tagerActivity);
    
    Suger getSuger();
}
```
## @Singleton
被标注的类将只存在一个实例。

```
//加了@Singleton之后提示这个接口将被多个线程共同操作。
@Singleton
@Component(module = {CoffeMachine.class})
public interface CoffeMakerComponent{
    //这个方法指明需要注解的地方
    TagerActivity inject(TagerActivity tagerActivity);
    Suger getSuger();
}

//@Singleton标注后将只会产生一个Heater实例
@Provides @Singleton Heater provideHeater(){
    return new Heater()
}

```

## @Scope 
自定义注解符，可以注解一个自定义的注解符来描述一个作用范围。

```
//下面注解了一个新的注解符——@PerActibity，使用这个注解符的地方作用范围将被限制在Activity的生命周期内。
@Scope
@Retention(RUNTIME)
public @interface PerActivity(){}
//下面注解了一个新的注解符——@PerFragemnt，使用这个注解符的地方作用范围将被限制在Fragment的生命周期内。
@Scope
@Retention(RUNTIME)
public @interface PerFragment(){}
```

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-89d539d78d295ef6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
