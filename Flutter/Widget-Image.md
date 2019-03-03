Image 用于展示图片。  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter-image.png)

# 1.创建 Image 

```
Widget _buildImage() => Center(
      child: Image.network(
        'https://raw.githubusercontent.com/chenBingX/img/master/其它/download.jpg',
        width: 200,
        height: 200,
      ),
    );
```  


看看效果：  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter-demo5.png)



# 2.Image 常用属性

- alignment → AlignmentGeometry: 图片的对齐方式。  

- color → Color：可以配合 colorBlendMode 修改图片颜色。  

- colorBlendMode → BlendMode：颜色混合模式。  

- filterQuality → FilterQuality：设置图片的质量。  

- fit → BoxFit：图片的填充方式。  

- height → double：设置图片的高度。  

- width → double：设置图片的宽度。  

- image → ImageProvider：图片提供者。  

- repeat → ImageRepeat：当图片不能充满 Image 时，如何充满。  


# 3.常用的创建 Image 的方式

1. `Image(@required ImageProvider image, ...)`  
    
    通过 ImageProvider 创建。比如：`NetworkImage(String url, ...)`


2. `Image.asset(String name, ...)`  
    
    从 asset 的相对路径创建。  
    
3. `Image.file(File file, ...)`  
    
    从文件创建。  
    
4. `Image.memory(Uint8List bytes, ...)`  
    
    从内存创建。  
    
5. `Image.network(String url, ...)`  

    加载一张网络图片。