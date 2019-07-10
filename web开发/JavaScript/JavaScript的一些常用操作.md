


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

# 跳转页面

```
window.location.href="artical.html";
```

# 修改 title

```
document.title="newTitle";
```

# 获取url参数

```
function getQueryString(name) {
    var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i");
    var r = window.location.search.substr(1).match(reg);
    if (r != null) return decodeURI(r[2]);
    return null;
}
```