使用 JavaScript 规范 Web 上创建的动画的方式。用于取代 SMIL 和 支持 CSS Transitions 及 Animations 的。


![](https://gw.alicdn.com/imgextra/i4/O1CN018kHuHm2A3pniIKDZv_!!6000000008148-2-tps-449-320.png)


在 Chrome 中，渲染线程分为 main thread 和 compositor thread。

css 动画中的 transforms 和 opacity 只需改变 compositor thread 就能完成。

而 js 动画会在 main thread 中执行，然后再触发 compositor。


main thread 只能中维护了一个 Layer 树，在 compositor thread 中也维护了同样的一棵树，它们是拷贝关系。

当 JavaScript 在操作 main thread 中的树时，compositor 中的树仍然可以进行渲染，事件会优先发到 compositor 中，然后再发到 main 中。

以下 css 修改不会触发 layout 或者 paint：

- visibility

- opacity

- perspective

- perspective-origin

- transfrom



`js+canvas(html5)+水平>css3>js+dom`


