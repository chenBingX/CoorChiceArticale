在 HTML5 中，支持通过 `<video>` 标签来播放指定 URL 的视频。

并且附带了一套播放控制组件，在各个浏览器可能样式不一样。

这是一个简单的例子：


```
<video src="https://www.runoob.com/try/demo_source/movie.mp4" controls></video>
```

这是效果（有的浏览器可能不支持啊！）：

<video src="https://www.runoob.com/try/demo_source/movie.mp4" controls></video>


在 `<video>` 标签中，通过 **src** 属性来指定视频文件的相对路径，或者绝对路径。


添加 **controls** 属性，可以获得视频播放控件器。

不同的浏览器支持的视频格式不一样，遇到不支持的视频格式，浏览器就不会播放视频了。

这时，你可以通过在 `<video>` 标签中，添加多个 `<source>` 标签，来引入不同格式的视频。

浏览器会从第一个 `<source>` 标签开始，寻找到它能支持的视频格式。

此外，你可以在 `<video>` 标签下增加文本或者图片，当遇到不支持视频播放的浏览器时，就会展示它们，来给予用户提示。

```
<video id="video1" width="320" height="240" autoplay controls>
    <!--MP4 格式的视频-->
    <source src="https://www.runoob.com/try/demo_source/movie.mp4" type="video/mp4">
    <!--ogg 格式的视频-->
    <source src="https://www.runoob.com/try/demo_source/movie.ogg" type="video/ogg">
    <!--文本提示-->
    您的浏览器不支持 HTML5 video 标签。
</video>
```

上面的代码运行起来看起来是下面这个样子的：

<video id="video1" width="320" height="240" autoplay controls>
    <!--MP4 格式的视频-->
    <source src="https://www.runoob.com/try/demo_source/movie.mp4" type="video/mp4">
    <!--ogg 格式的视频-->
    <source src="https://www.runoob.com/try/demo_source/movie.ogg" type="video/ogg">
    <!--文本提示-->
    您的浏览器不支持 HTML5 video 标签。
</video>

<hr>

上面的例子中，使用到了一个 **autoplay** 的属性。

这个属性可以让浏览器在加载好视频资源后，能够自动开始播放视频。

有的时候，这个属性可能失效！

因为部分浏览器不允许在非静音的条件下自动播放视频。

这个时候，你只需要再为 `<video>` 标签增加一个 **muted（静音）** 属性，你的视频也许就能自动开始播放了。

<hr>

通过 **JavaScript** 可以动态的控制视频控件的播放、大小等。

```
<div style="text-align:center">
  <button onclick="playPause()">播放/暂停</button>
  <br>
  <video id="video2"  style="margin-top: 10px" width="420" controls>
    <source src="https://www.runoob.com/try/demo_source/mov_bbb.mp4" type="video/mp4">
    <source src="https://www.runoob.com/try/demo_source/mov_bbb.ogg" type="video/ogg">
    您的浏览器不支持 HTML5 video 标签。
  </video>
</div>

<script>
var myVideo=document.getElementById("video2");

function playPause()
{
	if (myVideo.paused)
	  myVideo.play();
	else
	  myVideo.pause();
}
</script>

```

运行起来效果是这样的：


<div style="text-align:center">
  <button onclick="playPause()">播放/暂停</button>
  <br>
  <video id="video2" style="margin-top: 10px" width="420" controls>
    <source src="https://www.runoob.com/try/demo_source/mov_bbb.mp4" type="video/mp4">
    <source src="https://www.runoob.com/try/demo_source/mov_bbb.ogg" type="video/ogg">
    您的浏览器不支持 HTML5 video 标签。
  </video>
</div>

<script>
var myVideo=document.getElementById("video2");

function playPause()
{
	if (myVideo.paused)
	  myVideo.play();
	else
	  myVideo.pause();
}
</script>

在上面的例子中，**JavaScript** 找到 `<video>` 控件后，可以控制它的播放暂停。

> [在这里能查询到更多 `<video>` 的属性和方法](https://www.runoob.com/tags/ref-av-dom.html)