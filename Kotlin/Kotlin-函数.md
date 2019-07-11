# 函数的一般写法

```
fun attack(damage: Int): Unit {
    println("damage = " + damage)
}
```
- 函数需要使用【fun】关键字来声明；
- 参数的书写格式是`name: type`，和Java的`type name`有较大区别；
- 返回值书写和Java有区别。Unit相当于返回“Void”，在Kotlin中，返回值为void是可以省略不写的。  

简化版：  
```
fun attack(damage: Int) {
    println("damage = " + damage)
}
```

## 参数支持默认值

```
fun run(velocity: Float, orientation: String = "北方", 
  distance: Float = 2000f, accelerate: Float) {
    // to run
}
```
看，多方便啊！我们可以直接给参数设置默认值！并且在调用的时候可以省略这些有默认值的参数。就像这样：

```
run(12f, "东方", 3000f, 0.5f)  //指定全部参数
run(12f, accelerate = 0.5f) //跳过使用默认值，这时需要指明给那个参数传值
```

## 可变长度的参数
在java中使用`String...arg`来表示长度不确定的参数，看看在Kotlin中怎么实现。

```
fun useSkill(vararg skill: Skill) {
    // use skills
}
```
使用**vararg**关键字修饰变量，表示该类型参数的数量可变。通常因该把它放在参数列表的最后方。看看调用的例子：

```
var skill1 = Skill("火球术")
var skill2 = Skill("寒冰箭")
var skill3 = Skill("龙息术")
useSkill( skill1, skill2, skill3)
```
# 参数范型
使用范型参数和抛去函数的语法，结构上和Java还是有些相似的。

```
fun <T> studySkill(skill: T):List<T> {
    var skillList = ArrayList<T>()
    skillList.add(skill)
    return skillList
}
```
主要就是在方法名前加范型声明。

# 几种特殊的函数

## 局部函数

```
fun useSkill(vararg skill: Skill) {
    // use skills
    fun preperSkill(duration: Float): Boolean {
        var count = 0
        while (count == 3) {
            count++
            useSkill()
        }
        return true
    }
    preperSkill(1f)
}
```
上面这段代码展示了如何使用局部函数。在函数中我们任然可以继续定义函数，这和Python有些相似，只是这里的函数并不能够作为一个对象。

## 成员函数
和Java类里的方法差不多性质，主要是为了对比下局部函数。

## 单表达式函数
像Python一样，Kotlin支持单表达式函数，就像这样：

```
fun adjustExp(exp:Int) :Int= (exp * 1.1).toInt()
```
简单的函数我们可以简化成这样。当编译器能够自动推导类型时，还可以再简化：

```
fun adjustExp(exp:Int) = (exp * 1.1).toInt()
```
就是把返回值类型省略。

## 扩展函数
Kotlin令人着迷的一个地方绝对包含了**扩展函数**。这个特性使得我们能够动态的为一个类扩展新功能，而无需继承重写。来看看怎么回事吧。

```
fun TextView.handleText(text: String) {
        if (text.length > 7) {
            var newText = text.substring(0, 7) + "..."
            setText(newText)
            //注意，这里相当于直接在TextView里哦！
        }
    }
```
上面这个函数，是我给TextView扩展的一个功能！是的你没有听错！见证奇迹吧！

```
tv_test.handleText("CoorChice为帅而生！")
```
当函数写好后，你会发现，TextView已经拥有这个函数了！  

一个类的扩展方法可以在编写它的地方使用，出了这个范围就不行了。但如果把扩展方法写在方法文件中，通过**import**导入后，可以导入的文件中使用。Amazing！

++**注意：在Kotlin中，一个函数就可以作为一个文件！**++ 你不必为了书写一个函数而专门创建一个类。事实上，你可以把公共函数写到一个文件中，而不用专门创建一个类似于**AppUtils**之类的类。

