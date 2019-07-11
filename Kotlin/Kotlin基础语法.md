# 导入操作
一般的导入的操作和Java一样。

```
package com.coorchice.kotlindemo

import android.os.Bundle

```
细心的同学可能已经发现了，还是有差别的。**Kotlin**在进行导入操作时是可以省略分号的！  

特别的，当你的导入的内容命名发生冲突时，你可以使用`as`关键字来重新命名冲突项，以便在本文件范围内使用。这点和**Python**有些类似啊，非常赞的特性。比如：
```
import TestJavaClass as A
// 现在TestJavaClass在类中的名字也可以叫做A

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        ...
        var test = A()
        // 是不是很爽的感觉！
        test.field = "as"
        println(test)

    }
}
```
# 可见性修饰符
知道了怎么导入文件，现在我们看看Kotlin中的可见性修饰符。

- **private** -- 意味着只在这个类内部（包含其所有成员）可见；
- **protected** -- 和 private一样 + 在子类中可见。注意不再是包可见哦！
- **internal** -- 能见到类声明的 本模块内 的任何客户端都可见其 internal 成员；
- **public** -- 能见到类声明的任何客户端都可见其 public 成员。这是默认的，可以省略。  

**internal**可能不太好理解来看个例子：

```
class A {
    internal var a = 1
}

class B(a: A) {

    init {
        println(a.a)
        //由于B能见到A的声明，所以A的属性a对B是可见的
    }
}

```

# 定义变量
## 声明一个变量
在Kotlin中，我们声明一个变量需要使用**var**或者**val**关键字来声明。看看它们有什么区别：
- **val**：  
  只读变量。类似与Java中被**final**修饰的变量，需要在声明或者构造函数中赋值，并且之后不能够再改变。
- **var**：  
  普通变量，这个没什么好说的。


## 显示指明类型
```
val hero: String = "剑圣" 
```
在Kotlin中，如果显示的指明变量的类型，那么格式是：`name: type`。
## 自动推导类型
```
val hero = "剑圣"
```
和Python一样，这是一种非常爽的特性！

# 这才叫语言：空安全
Kotlin中，如果在声明变量的时候，在类型后面加个`“?”`，那么意味着这个变量可以为null。看个实际例子理解吧。

```
var skillDescription = "当心！这个技能强大到可能会伤到你自己！"

var skillDescription: String = "当心！这个技能强大到可能会伤到你自己！"
```
像上面这样常规的定义的变量，你尽可以大胆的使用而不用担心会出现空指针错误，因为Kotlin默认是不可为空的！比如：
```
skillDescription.length
```
等同于
```
var skillDescription: String？ = "当心！这个技能强大到可能会伤到你自己！"

if(skillDescription != null) {
    skillDescription.length
}
```
如你所见，加了`“?”`后，`skillDescription`才有可能为null。  

那么，对于上面这种可能为空的变量，Kotlin任然为我们提供了安全调用的操作符`**?.**`。
```
var skillDescription: String？ = "当心！这个技能强大到可能会伤到你自己！"

skillDescription.length
//这么调用是不安全的，并且编译器也会提示你。
//因为skillDescription是可能为null的。

//但是这么调用是完全安全的
// 它只有在skillDescription不为null时才会执行，所以避免了崩溃。
skillDescription?.length                                                                          
```
# 方便的字符串模版
Kotlin主打简洁、高效的开发，这个特性甚至渗入到了字符串模版中。
```
var heroName = "剑圣"
print("heroName: $heroName")
```
输出：

```
heroName: 剑圣
```

你可以直接在字符串中使用`$`加上变量明，就能直接输出变量的值了。想想Java中又臭又长的String.format()吧，它难道不能引起你的胃部不适吗？  

等等，还有些高级用法。
```
var skill1 = Skill("火球术")
print("skillName: ${skill1.name?.replace("火球术", "寒冰箭")}")
```
输出：

```
skillName：寒冰箭
```
你甚至可以在字符串模版中进行一些逻辑操作，通过使用`${do something...}`语法。就像上面那样。先取出skill1对象的name属性，然后再name字符串的把“火球术”替换成了“寒冰箭”，然后再输出。爱不释手的特性！有种**DataBinding**在xml中写代码的感觉。
