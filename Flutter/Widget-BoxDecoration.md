BoxDecoration 被用于装饰绘制区域。  

可以设置绘制区域的背景颜色、背景图片、形状、边框等效果。  

# 1.创建 BoxDecoration

```
main() => runApp(Center(
      child: Container(
        width: 400,
        height: 400,
        decoration: BoxDecoration(
            // 背景色
            color: Colors.white,
            // 绘制区域颜色
            shape: BoxShape.circle,
            // 图片
            image: DecorationImage(
                image: ExactAssetImage("images/flutter_logo.png"),
                fit: BoxFit.cover),
            // 边框
            border: Border.all(color: Colors.lightBlueAccent, width: 8)),
        child: Center(
          child: Text(
            "Box Decoration",
            textDirection: TextDirection.ltr,
            style: TextStyle(
                color: Colors.black, fontSize: 26, fontWeight: FontWeight.bold),
          ),
        ),
      ),
    ));
```  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter-logo2.png)  

# 2.BoxDecoration 常用属性

1. backgroundBlendMode → BlendMode: 背景色的混合模式。默认为 `[BlendMode.srcOver]`。

2. border → BoxBorder: 边框。  
    
    ```
    Border.all(width: 10, color: Colors.red)
    ```

3. borderRadius → BorderRadiusGeometry: 边框的宽度。  

    ```
    BorderRadius.all(Radius.circular(8)
    ```
    
4. color → Color: 设置绘制区域的背景颜色。会充满整个区域。  

5. gradient → Gradient: 设置绘制区域渐变色。会充满整个区域。  

6. image → DecorationImage: 绘制图片，层级是在 color 和 gradient 之上的。  

7. shape → BoxShape: 绘制区域的形状。默认为 `BoxShape.rectangle`。  



