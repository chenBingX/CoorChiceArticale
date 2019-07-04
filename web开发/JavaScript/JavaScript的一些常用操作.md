


# 滚动到指定元素位置

```
document.getElementById("id").scrollIntoView(true);
```

# 改变文本元素内容

```
element.innerHTML="新的内容";
```

# 在最后面添加元素

```
// 创建一个 <p> 元素
var element = document.createElement("p");
// 添加在最后
document.body.appendChild(element);
```

# 获取图片大小

```
var img = new Image();
img.src = "test.png";
img.onload = function(){
    var w = img.width;
    var h = img.height;
}
```