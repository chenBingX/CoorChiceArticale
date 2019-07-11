# if语句
用法和Java基本差不多。但Kotlin中的if语句可以作为表达式使用。

```
var equipmentLevel = if (Math.random() in 0.01..0.05) "史诗级" else "精良级"
```
如你所见，Kotlin中的if语句作为表达式时是可以有返回值的。
# while循环
用法和Java一样。
# for循环
在Kotlin中，for循环是这样写的。
```
for (a in 1..100) {
}
```
使用**in**关键字在[1, 100]范围内循环。

```
var list = listOf<Int>(1, 2, 3, 4, 5, 6)
for (a in list){
    print("a = $a")
}
```
循环遍历一个数组。
```
for ((index, value) in list.withIndex()){
    print("$index: $value")
}
```
也可以这样遍历，同时取得index和value。
# when语句
Kotlin明智的去掉了笨拙而丑陋的switch语句，同时创建了when语句来替代它。看看when语句有多迷人吧。
```
var t = when(skillName){
    "暴风雪", "烈焰风暴" -> { } 
    in "技能整列——1" -> { }
    !in "技能整列——2" ->{ }
    else -> {
        "火球术"
    }
}
```
Kotlin的when语句具有极高的灵活性，并且可以有返回值！  

when语句会检测每一种情况，直到成功匹配为止。如果没有匹配到，则执行`else{ }`代码块。在when语句中，必须包含**else**。
