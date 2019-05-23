常用的 HTML DOM 事件：

通过`window.addEventListener()`、`document.addEventListener()` 或者 `element.addEventListener()` 可以注册文档或元素的事件监听器。

```
element.addEventListener("click", displayDate);
```

或者，一些 HTML 元素本身就包含一些事件：

```

<div onmouseover="" onmouseout=""></div>
<button onclick=""></button>
<input onchange="">
```

# 鼠标事件

|属性|描述|
|---|---|
|onclick|点击事件|
|oncontextmenu|点击鼠标右键|
|ondblclick|双击某个对象|
|onmousedown|鼠标按下事件|
|onmouseenter|鼠标移到进入元素时触发|
|onmouseleave|当鼠标移出元素时触发
|onmousemove|鼠标移动时触发|
|onmouseup|鼠标按键被松开|
|onmouseover|鼠标移动到元素上时|
|onmouseout|鼠标移动出元素上时|

```
<div onmouseover="mOver(this)" onmouseout="mOut(this)" style="background-color:#D94A38;width:120px;height:20px;padding:40px;"></div>
<script>
function mOver(obj){
	obj.innerHTML="Thank You"
}
function mOut(obj){
	obj.innerHTML="Mouse Over Me"
}
</script>
```

# 键盘事件

|属性|描述|
|---|---|
|onkeydown|按下键盘上某个按键|
|onkeypress|按下键盘按键松开|
|onkeyup|键盘按键松开|

# 剪切板事件

|属性|描述|
|---|---|
|oncopy|复制时触发|
|oncut|剪切时触发|
|onpaste|粘贴时触发|

# 框架/对象事件

|属性|描述|
|---|---|
|onabort|图像加载被中断|
|onbeforeunload|页面即将离开或者刷新时触发|
|onunload|页面离开页面时触发|
|onerror|页面加载失败时触发|
|onload|一次加载完成时触发，文档，或者图片|
|onpageshow|页面变为显示时触发|
|onpagehide|即将跳转下一个页面前触发|
|onresize|页面大小变化时触发|
|onscroll|页面滚动时触发|


# onchange事件

常用于输入框，当输入内容确认改变（回车、离开输入框）时，会触发：


```
<input type="text" id="frame" onchange="upperCase()">
```