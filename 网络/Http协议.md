
# Http简介
Http是建立在TCP/IP之上的应用层协议，端口号为80。该协议是以ASCII码进行传输的，属于无状态链接（即每个Http请求都是独立的）。 

# Http请求体
Http请求体分为三部分：状态行、请求头、消息主体。  


```
<method> <>request-URL> <version>
<headers>

<entity-body>
```

# Http请求的4种交互方法
Http有4种交互方式PUT、DELETE、POST、GET，对应增、删、改、查。

## GET
get请求用于查询数据，不会影响资源状态。

```
GET /book/?sex=man&name=coorchcie HTTP/1.1
Host: www.exmple.com
User-Aengt: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7.6) Gecko/20050225 Firefox/1.0.1
Connection: Keep-Alive
```
- Get请求是安全的，Get请求一般不会产生副作用，不会影响资源状态。
- Get请求是幂等，就是说对于同一个URL的多次请求结果是一样点。  

注意上面的请求报文，请求数据是直接放在URL里的。Get请求的报文长度是有限制的，通常为1024个字节。  

## POST
post请求可以修改服务器上的资源。

```
POST / HTTP/1.1
Host: www.exmple.com
User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7.6) Gecko/20050225 Firefox/1.0.1
Content-Type: application/x-www-from-urlencode
Content-Length: 40
Connection: Keep-Alive

sex=man&name=coorchice
```
注意上面的报文段，请求数据必须放在body里，也就是对应`<entity-body>`。所以，Post请求通常更麻烦一些。Post请求的报文长度是没限制的。比如，发送文章评论通常应该使用Post请求，因为对Service资源产生了修改。  

post请求并没有规定body请求体中的数据要使用什么编码格式的数据，所以通常需要在`<headers>`中加上`Content-Type`标记，告诉服务器如何解析数据。  

### Content-Type数据编码格式
#### application/x-www-from-urlencoded
这是最常见的一种编码格式，通常浏览器会原生支持这种<from>表单格式。如果没有设置请求的enctype，默认会使用这种格式。  

<entry-body>像这样

```
sex=man&name=coorchice
```

#### application/JSON
使用JSON格式解析，使用比较多。

#### text/html
使用xml格式解析，比较臃肿。


#### multipart/form-data
使用这种编码格式，必须设置enctype为`multipart/form-data`。上传文件需要使用这种格式。

<headers>中这样定义

```
Content-Type: mutipart/form-data; boundary=[自定义分割线，比如————————哈哈]
```

<entry-bady>中数据像这样

```
————————哈哈
Content-Disposition: form-data; name="imageName"; fileName="imageName.png"
ContentType: image/png
<注意这里需要空两行1！>
<注意这里需要空两行2！>
...二进制数据...
<空一行！>
————————哈哈--
```
解释：
1. 首部需要使用<headers>中定义的boundary分割线；
2. name="文件名"，需要两端保持相同，做校验
3. fileName="文件路径"，指定文件保存在接收端的什么位置，一般不适用，会由接收端自己控制
4. <entry-body>中ContentType指明要传输的文件格式，如`image/png`表示png格式的图片数据
5. 结尾格式: 分割线+`--`
6. 注意中间的空行。

# 响应报文
Http的响应和Http的请求结构相似，分为：状态行、响应头、响应正文。  

状态行由协议版本、数字形式的状态码及相应描述组成。

```
<version> <code> <discreption>
```
下面是常见状态码：

code | 解释
---|---
200 Ok | 客户端请求成功
304 Not Modified | 文件未修改，可以直接使用缓存
400 Bad Request | 客户端请求语法错误，不能被服务端解析
403 Forbidden | 服务器收到请求，但拒绝提供服务。服务器通常需要在响应正文中包含原因。
404 Not Found | 请求资源不存在，比如URL输入错误。


```
HTTP/1.1 200 Ok

Server: Apache Tomcat/5.0.12
Date: Mon,60ct2003 13:23:42 GMT
Content-Length: 112

<响应正文>
```

# 条件Get
当客户端已经请求过一次资源，并打算再次请求时，可以使用条件Get，如果服务器没有更新过资源，可以直接使用本地缓存，这样Server就不用再把数据发送一次给客户端。如果资源发生了更新，那么服务器会把响应数据发送给客户端。  

客户端发起条件Get请求：

```
GET / HTTP/1.1
Host: www.exmple.com
If-Modified-Since: Thu, 4 Feb 2010 20:39:13 GMT
Connection: Close
```
服务器收到上面这个请求后，会根据`If-Modified-Since`字段的时间判断响应文件有没有更新过，如果没有更新过，服务器会返回`304 Not Modified`告知客户端没有更新，可以使用缓存。  


```
HTTP/1.1 304 Not Modified
Date: Thu, 04 Feb 2010 12:38:41 GMT  
Content-Type: txt/html
Expires: Thu, 04 Feb 2010 12:39:41 GMT
Last-Modified: Thu, 04 Feb 2010 12:29:04 GMT    //最后一次更新时间
Age：28
X-Cache: HIT from sy32-21.sina.com.cn 
Connection： Close
```

# 持久链接
在HTTP 1.1中，Keep-Alive保持长链接是默认开启的，除非在请求中加入`Connection: Close`才能关闭。这样做的好处是短时间内的请求不必重复建立／关闭链接。当然，Keep-Alive机制是有超时机制的，超过一定时长没有使用就会关闭。  

在使用了Keep-Alive机制后，如何判断一次会话的结束呢？   

1. 通过检查接收到的数据大小和`Content-Length`的内容是否相同。
2. 分块传输的话，最后一个chunck块为空，表明这次传输结束。

# 会话跟踪
通常服务器需要跟踪同一个用户的一系列会话，来标识这些请求是由同一个用户发起的。  

通常进行会话跟踪的手段是使用Cookie。Server会在响应报文中加上一小段Cookie信息，客户端可以把它保存在本地，每次请求的时候将Cookie放到<headers>中，服务端收到报文后，会读取Cookie，标识对应的用户。  

比如，登陆成功后，Server通常会给客户端返回的响应中加入Cookie，客户端保存后，下次请求时带上Cookie，表示登陆有效。  


Cookie通常会设置有效时长，如果失效，服务器会告知客户端，然后客户端需要重新登陆。