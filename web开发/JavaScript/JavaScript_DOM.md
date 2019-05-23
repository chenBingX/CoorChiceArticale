# HTML DOM 文档对象模型

![](https://gw.alicdn.com/tfs/TB15cEyXRSD3KVjSZFqXXc4bpXa-486-266.gif)

# HTML 中的节点

1. 整个 HTML 文档是一个 Document 对象，它是文档的 **根节点**。
2. 任何 HTML 元素都是一个 **元素节点**。
3. 任何 HTML 中的属性都是一个 **属性节点**。
4. 注释是 **注释节点**。
5. 文本插入到 HTML 元素中就成为一个 **文本节点**。

## Document 对象

Document 是 HTML 的根节点。

通过 Document 对象可以获取到整个 HTML 中的任何节点对象。


以下是 Document 常用的属性和方法：

|属性/方法|说明|
|---|---|
|document.images|文档中的所有 Image 对象集合|
|document.title|文档标题|
|document.URL|文档的完整URL|
|document.scripts|文档的完整所有 JavaScript 集合|
|document.activeElement()|获得当前的焦点元素|
|document.baseURI()|获得文档的绝对URI|
|document.body()|获得文档的 body 节点元素|
|document.cookie()|文档的所有cookie信息|
|document.documentElement()|获取文档的根节点|
|document.documentURI()|设置/获取文档的位置|
|document.domain()|获取文档的域名|
|document.getElementByClassName()|返回文档中所有指定类名的元素集合|
|document.getElementByTagName()|返回文档中所有指定标签名称的元素集合|
|document.getElementById()|返回指定 id 的元素|
|document.getElementByName()|返回指定名称的对象集合|
|document.createAttribute()|创建一个属性节点|
|document.createComment()|创建一个注释节点|
|document.createDocumentFragment()|创建一个空的 DocumentFragment 对像|
|document.createElement()|创建一个元素节点|
|document.createTextNode()|创建文本节点|
|document.write()|向文档中写入内容|
|document.writeln()|向文档中写入一行内容|
|document.open()|打开一个流，用来收集任何 write() 的数据|
|document.close()|关闭 open() 打开的流|
|document.addEventListener()|向文档添加事件监听器。[事件类型](https://developer.mozilla.org/zh-CN/docs/Web/Events)|
|document.removeEventListener()|移除一个监听器|


### 如何查找DOM元素？

JavaScript 通过 `document` 对象可以获取到 HTML 中的元素。


#### 通过 id 查找

通过标签id可以找到 HTML 中对应的元素。

```
var element = document.getElementById("elementId");
```

#### 通过标签查找

```
var element = document.getElementById("elementId");

// 找出 element 下的所有标签名为 p 的元素
// y会是一个数组
var y=element.getElementByTagName("p");

```

#### 通过类名查找

```
<p class="intro">O..houhou!</p>

x=document.getElementByClassName("intro");
// 查找到的是指定类名的元素集合
x[0].innerHTML="哈哈哈"
```

## Element 元素对象

一个 Element 代表着一个元素对象，如节点、注释等。

元素都有属性 `attr` 对象。

Element 的常用属性和方法：

|属性/方法|说明|
|---|---|
|element.id|element的id|
|element.parentNode|父节点|
|element.innerHTML|element的内容|
|element.ownerDocument|element所在的根节点|
|element.attributes|element的属性集合|
|element.childNodes|element的子节点集合|
|element.children|element的子元素集合|
|element.className|element的类名|
|element.clientHeight|element的可视高度|
|element.clientWidth|element的可视宽度|
|element.offsetHeight|element的高，包括边框|
|element.offsetWidth|element的高宽，包括边框|
|element.offsetLeft|element在父容器中的左边距|
|element.offsetTop|element在父容器中的上边距|
|element.scrollHeight|element的完整高度|
|element.scrollWidth|element的完整宽度|
|element.scrollLeft|element在父容器中的实际左边距
|element.scrollTop|element在父容器中的实际上边距|
|element.cloneNode()|可以一个元素|
|element.focus()|设置焦点到元素上|
|element.getAttribute()|获得指定属性|
|element.getAttributeNode()|获得指定属性节点|
|element.hasAttribute()|检查是否包含属性|
|element.hasFocus()|检查是否获得焦点|
|element.compareDocumentPosition()|比较两个元素的位置|
|element.addEventListener()|向元素添加一个事件监听器|
|element.removeEventListener()|移除一个事件监听器|
|element.appendChild()|向element中添加一个子元素|
|element.replaceChild()|替换element的一个子元素|
|element.removeChild()|移除element中的一个子元素|
|element.insertBefore()|将一个元素插入到另一个元素之前|



## Attr 属性对象

Attr 对象代表了一个属性对象。

|属性/方法|说明|
|---|---|
|attr.isId|检测是否是id属性|
|attr.name|属性名称|
|attr.value|属性值|
|attr.specified|如果属性被指定返回true，否则返回false|
|nodemap.length|节点列表的节点数|
|nodemap.getNameItem()|从节点列表中获取指定属性|
|nodemap.removeNamedItem()|删除指定属性节点|
|nodemap.setNamedItem()|设置指定属性节点|

