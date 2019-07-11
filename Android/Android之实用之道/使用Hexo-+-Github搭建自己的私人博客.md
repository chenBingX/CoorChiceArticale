> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。


# 背景介绍
  也许你已经在CSDN、简书等博客网站上创建了自己的博客。但作为程序猿，总想能够拥有一个属于自己的，能够自由DIY的私人博客。本篇文章，我将详细讲解如何搭建起一个私人博客。  
  
  
 # 动手搭建自己的私人博客吧！
 先上一张效果图: [CoorChice的Blog](https://chenbingx.github.io/)   
 
 
 ![CoorChice的blog](http://upload-images.jianshu.io/upload_images/1869462-3cee32c8c0ee3d8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
 
 看起来还不错吧，简约而专注于写作。我们来看看如何实现吧。  
 
## 准备阶段
首先，我们需要现安装一些东西。

### Homebrew
[Homebrew官网](http://brew.sh/index_zh-cn.html)  

> Homebrew是Mac OSX上的软件包管理工具，能在Mac中方便的安装软件或者卸载软件，相当于linux下的apt-get、yum神器；Homebre可以在Mac上安装一些OS X没有的UNIX工具，Homebrew将这些工具统统安装到了 /usr/local/Cellar 目录中，并在 /usr/local/bin 中创建符号链接。  

我们只需要打开终端，输入以下命令，确定执行后就可以安装**Homebrew** 了。

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
为什么需要安装Homebrew？是为了接下来方便的安装Node.js  

### Node.js  

由于我们使用Hexo框架来搭建生成博客，而Hexo是基于Node.js的。所以，还需要安装Node.js。
安装Node.js只需要在终端中输入：  

```
brew install node

```
安装完成后，输入：  

```
node -v
```

显示版本号表示已经安装完成，如下：

```
MacBook-Pro:~ ******$ node -v
v7.4.0
```

Node.js中有一个叫做npm(Node Package Manager)的工具，用于搜索、下载、管理Node的相关套件。有了这个东西，我们就能够方便的安装Hexo了。  

### Hexo 
[Hexo官网](https://hexo.io/zh-cn/)  

终于到主角儿出场了。  
> Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。   

安装Hexo也十分简单。在终端输入以下命令即可：   

```
sudo npm install -g hexo-cli
```

安装完成后，输入`hexo -version` 如果输出以下类似信息表示安装完成。


```
hexo-cli: 1.0.2
os: Darwin 16.3.0 darwin x64
http_parser: 2.7.0
node: 7.4.0
v8: 5.4.500.45
uv: 1.10.1
zlib: 1.2.8
ares: 1.10.1-DEV
modules: 51
openssl: 1.0.2j
icu: 58.2
unicode: 9.0
cldr: 30.0.3
tz: 2016j
```

### hexo-deployer-git自动部署发布工具
这个工具能够帮助我们快速部署博客bing并发布，安装它只需要在终端输入：  
```
npm install hexo-deployer-git --save
```

### Github仓库  
我们需要到自己的Github中创建一个仓库(还不会使用Github的同学百度、Google )，注意名字一定要是这种格式：

```
你的Github账号的注册名称.github.io 
```
比如我的博客地址：  
[https://chenbingx.github.io/](https://chenbingx.github.io/)  

这个名称将成为以后你的博客地址。谨慎啊！  

## 配置博客  

到此我们已经准备好了搭建博客的所有原材料，下面我们真的要开始搭建了啊 
1. 创建本地博客仓库  
使用命令:  

```
hexo init 你的Github账号的注册名称.github.io 
```
然后会在用户目录下生成一个名为 “你的Github账号的注册名称.github.io” 的文件夹，这个目录就是我们的博客目录。  

2. 设置主题  

首先，我们一路`cd`到刚刚创建的目录下。先下载一个主题，这是一个示例，都是一个套路。  

```
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

这里我使用的是NexT这个主题，文档十分详细。  
[NexT官网](http://theme-next.iissnan.com/)

然后我们在“你的Github账号的注册名称.github.io”目录下找到`_config.yml`文件，打开它(我是用Sublim Text打开的)。`command + F`搜索到`theme: `，在后面加上`next`，这是我们刚刚存放NexT主题的文件夹名称。  

接着需要把这个博客和刚刚创建的Github仓库关联。  
`command + F`搜索`deploy:` 然后按照以下格式设置：  

```
deploy: 
  type: git  //我们使用Git管理
  repo: git@github.com:****/****.github.io.git  //刚刚创建的Github仓库的SSH类型地址，使用HTTPS很有可能遇到权限问题
  branch: master  //你所期望的分支名称
```
注意，以上操作中，:号后面的空格一定不能省略。其他详细配置可以参考**Hexo** 的官网。   

3. 写文章  
我们的文章存放在博客根目录下的source/_posts文件夹下。我们可以在这个文件下建立子文件夹管理文章分类。文章类型被要求必须是`.md`格式的，就是Markdown。  
这里写Markdown与平时稍有不同，需要在头部添加：  

```
---
文章配置信息。
这里的配置信息与我们所使用的主题有关。
---

从这里开始写正文内容...
```

## 发布博客  
注意，以下操作需要`cd`到博客目录下。  

```
hexo clean  //每次有大变动，比如更换主题等操作时，可以清理以下之前的缓存；  

hexo g  //即hexo generate，生成静态文件；

hexo s  //即hexo server，启动服务器。默认情况下，访问网址为： http://localhost:4000/； 

hexo d  //即hexo deploy，部署网站到Github。

```


# 总结
终于，我们的博客搭建完成了。我们不需要租用服务器就搭建了一个属于自己的博客，并且在Hexo框架的支持下，我们可以使用不同的主题来装饰自己的Blog。


# 参考文献
1. [Homebrew官网](http://brew.sh/index_zh-cn.html)  
2. [NexT官网](http://theme-next.iissnan.com/)
3. [Hexo官网](https://hexo.io/zh-cn/)
  

![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-730440489638840a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
