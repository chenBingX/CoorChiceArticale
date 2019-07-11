# 类的声明
同Java、Python等一样，Kotlin使用**class**关键字来声明一个类。
```
class Skill {
    
}
```
创建一个对象实例：

```
val fireBallSkill = Skill()
```
Kotlin中创建对象不需要**new**。

# 构造函数
## 主要构造函数
```
class Skill public constructor(var name: String = "未知") {
   
}
```
Kotlin的主构造函数很特别，就像上面那样。既然是函数，我们同样可以为它指定默认值。参见[【Kotlin-函数：http://www.jianshu.com/p/3a815c51dcb5】。](http://www.jianshu.com/p/3a815c51dcb5)如果你不需要可见性修饰符（不写表示public），可以省略**constructor**。关于可见性修饰符参见[【Kotlin基础语法：http://www.jianshu.com/p/bc8896914df8】](http://www.jianshu.com/p/bc8896914df8)。那么，上面的类声明将会简化成这样：

```
class Skill(var name: String = "未知") {
   
}
```
如果你足够细心的话，也许你已经注意到了在 **【主构造函数】** 的参数前有一个 **var** 关键字。函数可不能这么搞！这又是Kotlin迷人的特性之一。通过这种主构造函数的语法，就可以完成对属性的初始化。  

它相当于：

```
class Skill(name: String = "未知") {
   var name = name
}
```
当然，这种特殊的主构造函数写法存在一个大问题！不知道你想到没？...不能在主构造函数里写代码了啊！WTF？  

别忙着喷，Kotlin没那么坑啦。看看怎么在初始化时添加逻辑。

```
class Skill(var name: String = "未知") {

   init{
       do something...
   }
}
```
**init{ }** 块中的代码会在初始化时被调用。虽然看起来有些奇怪，但是习惯就好。就像 **static{ }** 一样喽。

## 次要构造函数
在Kotlin中，次要构造函数有一个恶心的规则，++**就是要显示委托给主构造函数（在主构造有至少一个参数的时候）**++。看看怎么回事：

```
class Skill public constructor(var name: String = "未知") {

    var mp: Int? = 0
    
    // 次要构造函数
    constructor(name: String, mp: Int?) : this(name) {
        this.mp = mp
    }
}
```
看，次要构造函数不得不显示的通过`this(name)`委托给主构造函数。当然，如果主构造函数没有参数，能够简化不少。

```
class Equipment {

    constructor(header: String, clothes: String) {
    
    }
}
```

# 继承
在Kotlin中，所有的类都会隐式的继承**Any**，就像Java中所有的类都继承**Object**一样。  

Kotlin在设计的时候遵循了《Effective Java》中的 第 17 条：++**要么为继承而设计，并提供文档说明，要么就禁止继承。**++ 所以Kotlin的一个类及其函数，默认是不可以继承。如果需要继承，必须加上**open**关键字。

```
open class Skill(var name: String = "未知") {
    var damage: Int? = 0
    var description: String? = "当心！这个技能强大到可能会伤到你自己！"
    var mp: Int? = 0
    constructor(name: String, mp: Int?) : this(name) {
        this.mp = mp
    }
    open fun effect() {
    }
}
```
Skill类和它的成员函数都想继承、重载的话，给它们加上**open**去吧。就上上面的代码一样。  

接下来，看看如何继承它。

```
class FireBallSkill : Skill {
    constructor(name: String) : super(name)
    constructor(name: String, mp: Int?) : super(name, mp)

    override fun effect() {
        super.effect()
    }
}
```
继承的格式就是`：`号后跟基类。并且，构造函数需要使用**super**关键字进行基类的初始化。  

重载方法Kotlin提供了关键字，不必要再用注解了。就像上面重载`effect()`函数一样。如果不想让**FireBallSkill**的`effect()`再被继承，只需要再添加**final**关键字即可。

对于像Skill这样有主构造函数的基类来说，还可以这样来写继承。

```
class FireBallSkill(name: String) : Skill(name) {
    constructor(name: String, mp: Int?) : this(name)

    override fun effect() {
        super.effect()
    }
}
```
这种情况下，你可以在子类的构造函数中初始化基类。那么，次要构造函数就可以直接委托给子类自己的构造函数进行初始化。   

需要注意，Kotlin有一个规则，就是一个子类的两个以上的超类中如果包含同名open函数，那么这个函数必须被重载，否则不知道该调用那一个函数。用个官方例子说明吧：

```
open class A {
    open fun f() { print("A") }
    fun a() { print("a") }
}

interface B {
    fun f() { print("B") } // 接口成员默认就是“open”的
    fun b() { print("b") }
}

class C() : A(), B {
    // 编译器要求覆盖 f()：
    override fun f() {
        super<A>.f() // 调用 A.f()
        super<B>.f() // 调用 B.f()
  }
}
```
注意一点，Kotlin的多继承（继承类、实现接口）使用`,`分隔。

## 属性也可以重载
在Kotlin中，成员属性是可以重载的。当然，需要使用**open**关键字。

```
open class Skill(var name: String = "未知") {
    open var description: String? = "当心！这个技能强大到可能会伤到你自己！"

}
```
和重载方法一样，Kotlin使用**override**关键字重载属性。

```
class FireBallSkill(name: String) : Skill(name) {
    override var description: String? = "纵火狂热分子最爱使用的技能。"
}
```

# 抽象类
同Java一样，Kotlin也使用**abstract**关键字来定义抽象类。

```
abstract class Actor{
    abstract fun move()
}

class Hero: Actor() {
    override fun move() {
    }
}
```
对于抽象类，**open**关键字可以省略。很明显，抽象类就是为了继承。

# object关键字
在Kotlin中，**object**关键字被用来声明一些特殊对象或类。
- 匿名内部类

```
tv_test.setOnClickListener(object : View.OnClickListener {
    override fun onClick(v: View?) {
    }
})
```
在Kotlin中，匿名内部类需要使用**object**关键字来申明，在`:`后跟超类。既然是匿名，千万不要尝试在**obejct**后加名字！
- 实现单例
单例模式的写法从未如此简单和安全。Java中各种繁杂的单例写法可以出门右转了。

```
object WeatherManager{

    fun rain(){

    }

    fun sun(){

    }
}
```
只需一个object关键字声明即可。看看如何使用单例。

```
WeatherManager.rain()
WeatherManager.sun()
```
简便的，直接用类名就可以调用。有点像在调用静态方法，但是单例对象像纯真的孩子口中的话一样真实的创建了。
- 伴生对象
如果上面的单例写在一个类中，并且加上**companion**关键字，那么这个单例就成为宿主类的伴生对象了。伴生对象是用来代替类似Java中的静态变量的，真正意义上的实现**面向对象**。

```
open class Hero: Actor() {
    override fun move() {
    }
    companion object Factory{
        fun create(): Hero = Hero()
    }
}
```
事实上，你还可以把伴生对象的名称**Factory**省略掉。看看如何使用。

```
val demoHero = Hero.create()
```
调用伴生对象的成员，就像在Java中调用类的静态成员一样。

# 枚举类
在Kotlin中，使用**enum**关键字将一个类声明为枚举类。
```
enum class Color(val rgb: Int) {
        RED(0xFF0000),
        GREEN(0x00FF00),
        BLUE(0x0000FF)
}
```
相比Java，多了个**class**关键字。

# 数据类

```
public class User{

    private String name;
    private int age;
    
    public void setName(String name){
        this.name = name;
    }
    
    public String getName(){
        return name;
    }
    
    public void setAge(int age){
        this.age = age;
    }
    
    public int getAge(){
        return age;
    }
}
```
这是Java中令人烦躁的数据类声明过程。是的，我们都特别怕写，即使在有工具帮忙的情况下，看着这么庞大的一个类也是让人难受的。看看Kotlin如何简化的吧。

```
data class User(var name: String, var age: Int)
```
使用**data**关键字，一行代码生成一个数据类。还有什么理由不爱Kotlin呢？

# 接口
上面你也看到了接口的运用，我们同样使用**interface**关键字来定义接口。注意，Kotlin中的接口和Java中有不小的差别。因为Kotlin中的接口是**可以有方法体**的！当然，有方法体的你不用去实现它。此外，Kotlin中的接口还能包含属性，当然它们是**抽象**的。你必须在实现接口的时候也实现这些个抽象属性。

```
interface Attack{
    var damage: Int

    fun attack()

    fun preRock(){

    }

    fun aftRock(){

    }
}
```
看一个实例吧。

```
class Mage: Attack{
    //在这实现了抽象属性
    override var damage: Int
        get() = 0
        set(value) {}

    override fun attack() {
    }

}
```

