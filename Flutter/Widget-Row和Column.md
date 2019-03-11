Row 和 Column 分别用于组织 **水平布局** 和 **垂直布局**。  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/row和column例子1.png)  

上图相对复杂的布局，就是使用 Row 和 Column 组织的。  

Row 和 Column 用法基本一致，只是 Row 是水平方向布局，Column是垂直方向布局。  

  

# 1.创建 Row

```
Widget _buildRowImg() => Row(
      // 主要对齐方式
      mainAxisAlignment: MainAxisAlignment.spaceEvenly,
      children: [
        Image.asset(
          'images/pic.png',
          width: 100,
          height: 100,
        ),
        Image.asset(
          'images/pic.png',
          width: 100,
          height: 100,
        )
      ],
    );
```

加入到 App 中：  

```
main() => runApp(MaterialApp(
    title: "Flutter Demo",
    home: Scaffold(
        appBar: AppBar(title: Text("Flutter Demo")),
        body: Container(
            color: Colors.grey[300], child: Center(child: _buildRowImg())))));
```

看看效果： 

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter-demo4.png)  


# 2.Row 和 Column 的常用属性 

- children → List<Widget>：子 Widget。  

- mainAxisAlignment → MainAxisAlignment：主轴方向的对齐方式。主轴：Row-水平，Column-垂直。  

- crossAxisAlignment → CrossAxisAlignment：副轴方向的对齐方式。如，当主轴是水平轴时，副轴是垂直轴。  


## 2.1 MainAxis 和 CrossAxis

- Row  
    
    ![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Row-axis.png)
    
    
- column  
    
    ![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Column-axis.png)


## 2.2 MainAxisAlignment 效果

- start   
    
   ![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/MainAxisAlignment-start.png) 


- end  
    
    ![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/MainAxisAlignment-end.png)


- center  
    
    ![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/MainAxisAlignment_center.png)


- spaceBetween: 中间留间隙。  
    
    ![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/MainAxisAlignment-spaceBetwee.png)


- spaceAround: 每个子 Widget 两边的间隙均分  
    
    ![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/MainAxisAlignment-spaceAround.png)


- spaceEvenly: 整体均分间隙  
    
    ![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/MainAxisAlignment_spaceEvenly.png)


## 2.3 CrossAxisAlignment 效果


- start: children在交叉轴上起点处展示  
    
    ![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/CrossAxisAlignment-start.png)
    
- end: children在交叉轴上末尾展示  
    
    ![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/CrossAxisAlignment-end.png)
    
- center：children在交叉轴上居中展示   
    
    ![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/CrossAxisAlignment-center.png)

- baseline：在交叉轴方向，使得children的baseline对齐

- stretch：让children填满交叉轴方向

