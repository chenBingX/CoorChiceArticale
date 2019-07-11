![image](http://upload-images.jianshu.io/upload_images/1869462-305e7c2a7059f197.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 人人可以编程
计算机发展至今，已经实现了普及化。相应的，驱动计算机的程序已经遍布了世界的每一个角落。过去，编写程序是件高门槛的事，整件事情对于普通人来说就像外星人一样陌生。  

但是现在，相信我，人人可以编程，甚至它应该作为你使用计算机的一种必备能力。整个网络世界里各种教程，编程语言也越来越人性化（有时候看一段程序，你甚至都会有一种看文章的感觉），丰富的库和封装让你对于一些复杂的东西可以拿来就用，而不必知道为什么。这些对于一个普通的计算机用户而言就够了。当你想要简化生活、提高效率时，你可以随时开始动手！  

是的，你只需要花上几个小时，也许可能是2、3天，稍微学习一门编程语言的基础语法，你就可以利用互联网的资源开始编写简化你生活的小工具了。对大多数人的个人小工具而言，也许你可以考虑把它做的美观一点，但我觉得关于代码的效率、优雅你不应该去考虑。对你而言，只要逻辑梳理通顺，程序跑起来，功能基本可以使用就够了。这就是为什么我说，花上一点时间学习一点基础语法就行的原因。  

嘿，这绝对是一件**低投入，高回报**的投资！

# 用编程简化生活
CoorChice作为一只靠编程吃饭的帅猿，自然要好好利用编程能力去简化生活、提高效率。一些繁杂的事情，它们可是体力活！我只需要写一个简单小程序去帮我完成就行。一劳永逸就是这么爽！  

比如我现在要举例的Deleter。

## Deleter，为批量处理而生
我之所以需要一个Deleter，是因为CoorChice发现，我总是不可避免的经常要对大量的文件名做些什么。删除文件名的一部分、替换文件名的一部分、为文件名添加新的成分等等简单，但是繁琐而消耗时间的事情。因此，CoorChice编写了Deleter来帮助我完成这些事情。原本我可能需要花上几个小时，甚至一天去做的体力活，现在只需要几秒钟！这就是我所说的简化生活。  

下面，CoorChice将向你展示Deleter是如何帮助我简化生活，提高效率的，并会附上它的实现代码。  

对于一些小工具，CoorChice会选择使用Python来编写。因为它像白话文一样简洁，并且有相当多的开源库可供使用。对于大多数而言，选择Python来作为简化生活的编程语言，看起来是很明智的。尽管不用去纠结那几毫秒的效率，忽略它吧。这就是我学习Python想要做的事。  

## 使用Deleter
你可以直接下载这个Python程序：  

[Deleter下载地址](http://ogemdlrap.bkt.clouddn.com/Deleter.py?attname=)  

或者复制文末的代码，然后保存为`.py`文件。  

运行它你只需要打开命令行，进入到Python程序所在的目录，然后输入：

```
python Deleter.py
```
接着，一个Python程序就被运行起来了。但是，有一个前提是你必须安装过Python环境。  

怎么安装来着？或许你该试着在网上找找所需要的信息。可以看看CoorChice的这篇文章，关于如何进行互联网搜索的：[【这十个搜索技巧至少价值100万！：http://www.jianshu.com/p/a2f0f5a39cc3】](http://www.jianshu.com/p/a2f0f5a39cc3)。  

一切顺利的话，你将能看到如下界面：  

![image](http://upload-images.jianshu.io/upload_images/1869462-082fb61a83a00d2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

### 批量替换文件名内容

![image](http://upload-images.jianshu.io/upload_images/1869462-ccaa42deccad6e62.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

上图是一个序列帧图的文件集合，它包含了很多很多张图片。现在我想要给它们重新命名。如果没有Deleter这个程序，想想看，这么多，我得一个个改。它将会花费我将近一个小时的时间！我经常面对着比这多的多的文件需要修改，如果没有Deleter，天知道有多少时间将花费多少时间在这些无意义的事情上。即使用上脚趾头和手指头一起去计算，也不能算的出来！  

现在，在Deleter中的【路径】输入框输入这些文件所在的目录完整地址；接着在【需要删除的内容】输入框输入：`mike`，即想要删除的的内容；然后在【替换的新内容】输入框输入：`newname`，即想要替换的新内容。就像这样：  

![image](http://upload-images.jianshu.io/upload_images/1869462-b53e252b6566552e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  


最后点击【Sure】。1s后：  

![image](http://upload-images.jianshu.io/upload_images/1869462-1813a71dde56fd2b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

嗯，成功替换了所有文件名。这次CoorChice省下了59分55秒的时间。

### 批量删除文件名的指定部分
基本操作和上面一样，你只需要把【替换的新内容】输入框空出来就行了。  

![image](http://upload-images.jianshu.io/upload_images/1869462-61d2ca6b61e2c057.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

点击【Sure】。  

![image](http://upload-images.jianshu.io/upload_images/1869462-0e8dac713cfa545c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

需要注意，如果文件名中包含多个与需要删除内容相同的字段，那么Deleter将会从后往前删除，并且每次只能删除一个地方的。

### 在文件名首端批量添加字段
这次需要把【需要删除的内容】输入框空出来，像这样：  

![image](http://upload-images.jianshu.io/upload_images/1869462-966b8e0c10d5dde5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

点击【Sure】。  

![image](http://upload-images.jianshu.io/upload_images/1869462-ccaa42deccad6e62.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

注意，新增的内容是从首端插入哦。  

# 实现代码
那么实现上面这些功能的程序的代码长什么样呢？

```
# coding=utf-8
from Tkinter import *
import os


def sure():
    global path
    path = path_entry.get()

    global suffix
    suffix = suffix_entry.get()

    global new_content
    new_content = new_content_entry.get()

    change_name()


def change_name():
    os.chdir(path)
    for file in os.listdir(path):
        if file.rfind(suffix) != -1:
            print 'before: ' + file
            old_name = file
            start_index = 0
            end_index = 0
            if len(suffix) > 0:
                start_index = file.rindex(suffix)
                end_index = start_index + len(suffix)
            if end_index != start_index:
                file_length = len(file)
                end_content = file[end_index:file_length]
                start_content = file[0:start_index]
                new_name = start_content + new_content + end_content
            else:
                new_name = new_content + file
            print '-----> after: ' + new_name
            os.rename(old_name, new_name)


# UI
root_view = Tk()
root_view.title('Deleter')
root_view.geometry("300x120")  # 是字母x，不是*！

path_label = Label(root_view, text='路径')
path_label.grid(row=0)
path_entry = Entry(root_view)
path_entry.grid(row=0, column=1)

suffix_label = Label(root_view, text='需要删除的内容')
suffix_label.grid(row=1)
suffix_entry = Entry(root_view)
suffix_entry.grid(row=1, column=1)

new_content_label = Label(root_view, text='替换的新内容')
new_content_label.grid(row=2)
new_content_entry = Entry(root_view)
new_content_entry.grid(row=2, column=1)

btn_sure = Button(root_view, text='Sure', command=sure)
btn_sure.grid(row=3, column=1)

root_view.mainloop()
```
大家可以看到，非常短的一段代码。但是它所实现的功能，一直以来帮助CoorChice节省了大量的时间，而我编写它只需要花上几分钟。当然，这段代码任然可以优化，但是我想我不会花时间去优化它。因为对于我而言，它已经解决了我的问题，并且所花费的时间我甚至可以忽略掉。所以，我不会花费时间去把代码优化的优雅，或者让它的执行效率能快上那么几毫秒。我想你知道我要说什么。  

# 开始编程吧
CoorChice之所以分享自己的简陋的小工具，其实是想说明，在这个时代，你只需要掌握简单的编程知识，你就能够通过编程来简化你的生活。有用的程序不一定需要多晦涩难懂的知识，或者像混乱的线球一样复杂的逻辑才能实现。你只需要一点点编程语言基础，加上一点点小逻辑，一道程序就能新鲜出炉了。而且一次编写，反复使用！  

**对于我们大多数人而言，这就够了。**  

# 感谢阅读，请点赞吧亲！
