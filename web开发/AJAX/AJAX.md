AJAX = Asynchronous JavaScript and XML，是一种前端用于请求网络的技术解决方案。

# 如何使用 AJAX 请求网络？

## 1. 创建 XMLHttpRequest 对象

```
var Http = new XMLHttpRequest();
```

## 2. 发送网络请求

```
// 第一个参数：请求方式，GET、POST 等
// 第二个参数：请求URL
// 第三个参数：是否异步
Http.open("GET", url, true);

// 发送请求
Http.send();

// 请求参数也可以放到 send() 函数中
Http.send("fname=Henry&lname=Ford");
```

通过 `send()` 实际上可以发送数据，支持的类型有：

- ArrayBuffer

- Blob

- Document

- DOMString

- FormData

- null

**如何设置请求头呢？**

```
Http.setRequestHeader("Content-type","application/x-www-form-urlencoded");
```

只需要设置就行了，它会放到一个 map 中的。


**如何设置超时？**

```
Http.timeout = 500;
```

单位为 **毫秒**。

## 3. 接收网络请求结果

如果是异步请求：

```
Http.onreadystatechange = function(){

    if(Http.readyState == 4 && Http.status == 200){
        var response = Http.responseText;
    }
}
```

**readyState** 表示请求的实时状态：

- 0：请求初始化完成

- 1：与服务端建立链接完成

- 2：请求已接收

- 3：请求处理中

- 4：请求已完成


**status** 表示响应结果状态：

- 200："OK"

- 404：未找到

如果是同步请求：

```
// 直接取
var response = Http.responseText;
```

获取请求结果有两种格式：

- responseText：文本格式

- responseXML：XML格式



# 如何获取上传、下载进度？

```
// 下载进度
Http.onprogress = function(event){
    if(event.lengthComputable){
        var completedPercent = event.loaded / event.total;
    }
}

// 上传进度
Http.upload.onprogress = function(event){
    if(event.lengthComputable){
        var completedPercent = event.loaded/ event.total;
    }
}
```

