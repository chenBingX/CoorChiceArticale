[参考地址](https://realm.io/cn/news/donn-felker-solid-part-4/)

- 接口的功能通常应该单一化，随着功能的增加，一个接口中的内容会太多，当通常你只需要使用其中一个功能时，却不得不把其它功能也摆出来，这是不科学的。所以接口的粒度一般应做最小化。
- 接口可以使用适配器模式来对功能进行筛选，同时又得相近功能够放在一起，又或者你对接口得扩展将变的不那么乱成一团了。
- 类依赖的接口应该更小、更具体。
```
public interface Move{
    //一期功能
    void walk();
    
    //二期功能
    void run()；
    
    //三期功能
    viod roll();
}

public abstract class MoveAdapter implements Move{
  
  public void walk(){};
  
  public void run(){};
  
  public viod roll(){};
}

接下来继承MoveAdpter就可以选择性去使用想要的功能。
```