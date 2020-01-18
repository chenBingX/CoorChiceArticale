GUI（Graphical User Interface）基本要素：
- 事件：来自系统、用户输入的事件
- 窗口：负责视图展示窗口的建立、销毁，视图内容的管理
- 渲染：负责绘制渲染窗口内容


Widget 是用来配置渲染对象的，
每个 Widget 都会有一个 Element


通过 Widget 来构造视图，Flutter 会在 Vysnc 信号到来时，通过 Widget 建造一棵 Element 树，由 BuildOwner 来负责这颗树的更新。  

如果是 RenderObjectWidget，会创建对应的 RenderObjectElement，同时在 mount 的时候，会创建对应的 RenderObject，给 Widget 设置的参数实际最后是作用与 RenderObject，
RenderObject 是真正布局和渲染的对象。



Flutter 是通过 Skia 渲染引擎自己绘制图形界面（组件）


WidgetsFlutterBinding 将 Framework 和 Engine 关联起来的对象。在 runApp() 执行后，这是一个单例。


window 是个单例，包含了窗口（设备）尺寸、像素密度等等一系列信息，监听一系列的绘制、设备属性变化

```
import 'dart:ui' as ui show saveCompilationTrace, Window, window;

final Window window = Window._();
```






通过 with 链接，可互相转化

包含了
GestureBinding,
ServicesBinding,
SchedulerBinding, 运行时任务调度
    scheduleWarmUpFrame 请求接收下一帧
PaintingBinding,
SemanticsBinding,
RendererBinding：关联 渲染树 和 Engine，
    创建 PipelineOwner，这相当于是一个渲染管道
        flushLayout
        flushPaint
    创建 RenderView extends RenderObject，是渲染树的root对象，持有了 window，初始化后，请求接收第一帧。注册 layout - performLayout、paint - paint()
        赋值的同时，把它赋值给了 PipelineOwner 的 rootNode
        ```
        set renderView(RenderView value) {
          assert(value != null);
          _pipelineOwner.rootNode = value;
        }
        ```
    addPersistentFrameCallback(_handlePersistentFrameCallback); 注册不可移除的 frame callbacks，能够触发 drawFrame()
    ```
    void drawFrame() {
      assert(renderView != null);
      pipelineOwner.flushLayout();
      pipelineOwner.flushCompositingBits();
      pipelineOwner.flushPaint();
      renderView.compositeFrame(); // this se
      pipelineOwner.flushSemantics(); // this
    }
    ```

WidgetsBinding：关联 Widgets 层和 Engine 的对象
    创建 BuildOwner 管理 Widget 树的构建工作
    创建 RenderObjectToWidgetAdapter，将传入的根 widget 作为 它的 child，container 是 RendererBinding 创建的 renderView，也就是根 RenderObject，而 child 会作为 renderView 的 child
    创建 renderViewElement 组件树的根 rootElement，将 RenderObjectToWidgetAdapter 赋值为它的 widget

    WidgetsBinding#drawFrame
        - BuildOwner#buildScope
            - Element#rebuild
                - Element#performRebuild: Element 的子类通常实现这个函数来组织自己的构建逻辑，如 ComponentElement 会调用特有的 build。StatelessElement 和 StatefulElement 都继承自 ComponentElement
                    ... RenderObjectToWidgetElement#performRebuild
                           - RenderObjectToWidgetElement#update
                               - RenderObjectElement#update
                                   - RenderObjectWidget#updateRenderObject
                               - RenderObjectToWidgetElement#_rebuild
                                   - Element#updateChild
                                       - Element-child#update(widget)
                                       - Element#inflateWidget(newWidget, ): 将 Element 插入 Element 树中
                                           - {
                                                final Element newChild = newWidget.createElement();
                                             }
                                           ... Element-child#mount
                                                   - {_depth = _parent != null ? _parent.depth + 1 : 1;}
                                                   - {_owner = parent.owner;}
                                           ... RenderObjectElement-child#mount
                                                   - Element#mount
                                                        - {_depth = _parent != null ? _parent.depth + 1 : 1;}
                                                        - {_owner = parent.owner;}
                                                   - {
                                                        // 由具体的 RenderObjectWidget 类实现 createRenderObject
                                                        _renderObject = widget.createRenderObject(this)
                                                     }
                                                   - RenderObjectElement#attachRenderObject
                                                        - {
                                                           _ancestorRenderObjectElement = _findAncestorRenderObjectElement();
                                                           // 插入 renderObject 到渲染树中
                                                           _ancestorRenderObjectElement?.insertChildRenderObject(renderObject, newSlot);
                                                           }
                                                        - RenderObjectElement#_findAncestorRenderObjectElement
                    ... ComponentElement#performRebuild
                            - ComponentElement#build
                                ... StatelessElement#build
                                        - StatelessWidget#build
                                ... StatefulElement#build
                                        - State#build
                            - Element#updateChild
                                - Element-child#update(widget)
                                - Element#inflateWidget: 将 Element 插入 Element 树中
                                    ... Element-child#mount
                                            - {_depth = _parent != null ? _parent.depth + 1 : 1;}
                                            - {_owner = parent.owner;}
                                    ... ComponentElement-child#mount
                                            - Element#mount
                                                - {_depth = _parent != null ? _parent.depth + 1 : 1;}
                                                - {_owner = parent.owner;}
                                            - ComponentElement#_firstBuild
                                            - Element#rebuild
        - super.drawFrame => RendererBinding#drawFrame
            - PipelineOwner#flushLayout
                - RenderObject#_layoutWithoutResize
                    - RenderObject#performLayout: 具体的 RenderObject 类实现 performLayout
                    - RenderObject#markNeedsSemanticsUpdate
                    - RenderObject#markNeedsPaint
                        - {
                            if (owner != null) {
                              owner._nodesNeedingPaint.add(this);
                              owner.requestVisualUpdate();
                            }
                            parent.markNeedsPaint();
                          }
            - PipelineOwner#flushCompositingBits
                - RenderObject#_updateCompositingBits
                    - RenderObject#markNeedsPaint();
            - PipelineOwner#flushPaint
                - {
                    PaintingContext.repaintCompositedChild(node);
                  }
                    - PaintingContext#_repaintCompositedChild
                        - RenderObject#_paintWithContext
                            - RenderObject#paint: 由具体子类实现
            - RenderView#compositeFrame: 提交渲染数据到 GPU
            - PipelineOwner#flushSemantics: 提交语意到系统


---


初始化：
1. 创建接收触摸事件的监听
GestureBinding: {
    window.onPointerDataPacket = _handlePointerDataPacket;
}

2. 创建用于和原生进行通讯的 BinaryMessenger。 MethodChannel 默认情况下，就是使用这个实例和原生通讯
ServicesBinding: {
    _defaultBinaryMessenger = createBinaryMessenger();
}

3. 初始化应用生命周期监听
SchedulerBinding: {
  SystemChannels.lifecycle.setMessageHandler(_handleLifecycleMessage);
}
SchedulerBinding 能够插入 SchedulerPhase 中几种类型的回调到回调队列中

4. 初始化 ImageCache，用于缓存图像数据，默认最大100mb，1000张，通过 maximumSize() 和 maximumSizeBytes() 可以调整。预热 'Skia' 引擎
PaintingBinding: {
  shaderWarmUp.execute();
}

5. 连接语义层和引擎
SemanticsBinding: {
    _accessibilityFeatures = window.accessibilityFeatures; 
}

6. 连接渲染树和引擎，创建 PipelineOwner，创建根 RenderObject ——  RenderView，添加用于触发渲染的回调 
RendererBinding: {
  _pipelineOwner = PipelineOwner(
    onNeedVisualUpdate: ensureVisualUpdate,
    onSemanticsOwnerCreated: _handleSemanticsOwnerCreated,
    onSemanticsOwnerDisposed: _handleSemanticsOwnerDisposed,
  );
  initRenderView();  // 初始化应用渲染树的根
  addPersistentFrameCallback(_handlePersistentFrameCallback);
}

RenderView#performLayout(){
  child.layout(BoxConstraints.tight(_size));  // 根 RenderObject 提供一个尺寸
}

`RenderBinding#handlePersistentFrameCallback()：frame 信号时调用`
  - WidgetsBinding#drawFrame()
    - buildOwner.buildScope(renderViewElement);   // 重新构建 renderViewElement 树中需要重建的 Element，即 _dirtyElements 中的 Element
       - _dirtyElements[index].rebuild() =》 Element#rebuild() // 让需要重建的 Element 重建
          - `Element#performRebuild(); // 由 Element 的各个子类实现自己的重建逻辑` **ComponentElement、RenderObjectElement**
    - super.drawFrame() => RenderBinding#drawFrame(){：RenderBinding 开始处理渲染相关的任务
        pipelineOwner.flushLayout(); // 更新所有需要重绘的 RenderObject 的布局信息，_nodesNeedingLayout[]
          - RenderObject#_layoutWithoutResize(){ // 让 RenderObject 执行布局，然后标记为需要绘制
              - `performLayout(); // 执行布局，由具体的 RenderObject 子类实现布局逻辑()`
              - markNeedsPaint(){
                owner._nodesNeedingPaint.add(this);  // 添加到需要重绘的树中
                owner.requestVisualUpdate();
              }
          }
        pipelineOwner.flushCompositingBits(); // 对 RenderObject 根据深度排序
          - visitChildren((RenderObject child) {  // 由具体的 RenderObject 子类实现
                  child._updateCompositingBits();
                  if (child.needsCompositing)
                    _needsCompositing = true;
                });
        pipelineOwner.flushPaint();{ // _nodesNeedingPaint 排序后依次绘制
          PaintingContext.repaintCompositedChild(node);
            - _repaintCompositedChild(
                     child,
                     debugAlsoPaintedParent: debugAlsoPaintedParent,
                   );
              - childContext ??= PaintingContext(child._layer, child.paintBounds); // 获得用于绘制的画布
              - child._paintWithContext(childContext, Offset.zero); // 在画布上绘制
                - `paint(context, offset); // 由 RenderObject 的子类实现绘制逻辑`
        }
        if (sendFramesToEngine) {
          renderView.compositeFrame(); //
          pipelineOwner.flushSemantics();
          _firstFrameSent = true;
        }
      }
        
7. 连接组件树和引擎，创建 BuildOwner，它负责维护需要重新创建的 Element
WidgetsBinding: {
  _buildOwner = BuildOwner();
}

WidgetsBinding#scheduleAttachRootWidget(Widget) // 调度一个Timer将用户根组件绑定到根布局上
  - WidgetsBindings#attachRootWidget(Widget) // 将用户根组件绑定到根布局上
    - _renderViewElement = RenderObjectToWidgetAdapter<RenderBox>(
        container: renderView,
        debugShortDescription: '[root]',
        child: rootWidget,
      ) // 创建根 Widget，将用户根组件作为它的 child，将根 RenderObject 作为它的父级 RenderObject
      .attachToRenderTree(buildOwner, renderViewElement as RenderObjectToWidgetElement<RenderBox>); // 创建根 Element，然后将用户根组件绑定到根 Element 上
        - element = RenderObjectToWidgetAdapter#createElement() //  创建根 Element，即父类 RootRenderObjectElement，只有这个类型的 Element 才能显式的设置一个 BuildOwner，所以它被作为 Element 树的根节点
        - element.assignOwner(owner); // 为根 Element 设置 BuildOwner，该 Element 下的子 Element 的 BuildOwner 都是同一个
        - owner.buildScope(element, () {
            element.mount(null, null); // 先将节点安装起来
              - RenderObjectToWidgetElement#rebuild // 一开始就调用创建，将用户根组件安装到应用根节点上
                - _child = Element#updateChild(_child, widget.child, _rootChildSlot);  // 安装\更新子节点
                // 1. 如果子节点不为null，但新配置（即 Widget）为null，会将子节点从 Element 树和渲染树中移除
                // 2. 如果子节点不为null，新配置和原来相同，不变；不同，更新配置 `_widget = newWidget;`
                // 3. 如果子节点为null，新配置不为null，调用 inflateWidget(newWidget, newSlot) 创建子节点，且添加到树中
                  - Element#inflateWidget(Widget newWidget, dynamic newSlot)
                    - final Element newChild = newWidget.createElement(); // 创建子节点
                    - newChild.mount(this, newSlot); // 将子节点安装到自己的树中
          }); // 更新 BuildOwner 管理的组件。此处初始化调用实际上只会重置一下组件的状态，不会触发构建。因为 _dirtyElements 还为空
        -  SchedulerBinding.instance.ensureVisualUpdate();  //  此处调用无效，因为 schedulerPhase 还在 SchedulerPhase.idle
          - scheduleFrame();      
        // 如果不是首次创建， element.markNeedsBuild(); 等待frame信号，然后渲染 
        
SchedulerBinding#scheduleWarmUpFrame()
  - handleBeginFrame(null);  //  绘制前
  - handleDrawFrame(); // 绘制       
    - PERSISTENT FRAME CALLBACKS：RenderBinding 初始化的时候注册了一个，后面添加的 PERSISTENT，会在绘制完后才调用
    - POST-FRAME CALLBACKS：因此，Post-Frame 是在一个 frame 信号触发完绘制之后才会调用


RenderObjectToWidgetElement#mount(Element parent, ){
  super.mount(parent, newSlot); // 先将自己安装到 Element 树 和 渲染树中
  _rebuild(); // 重建
}


RenderObjectElement#mount(Element parent, ){
  super.mount(parent, newSlot); // 将自己安装到 Element 树中
  _renderObject = widget.createRenderObject(this); // 创建自己的 RenderObject
  attachRenderObject(newSlot); // 将自己的 RenderObject 添加到渲染树中
}

Element#mount(Element parent, ){
  _parent = parent; // 设置父节点
  _depth = _parent != null ? _parent.depth + 1 : 1; // 设置深度
  _active = true; // 设置为活跃态。说明一个 Element 调用 mount 绑定到 Element 树上之后，就会处于 活跃态
  _owner = parent.owner; // 设置 BuildOwner
}

ComponentElement#mount(Element parent,){
  super.mount(parent, newSlot); // 先将自己安装到树中
  _firstBuild()
    - rebuild(); // 
      - ComponentElement#performRebuild(){
        Widget built = build(); // 通过 build() 获得 Widget 组件
        _child = updateChild(_child, built, slot); // 用 built 创建自己的子 Element，如果已经有了，就是更新它
      }
}

RenderObjectElement#mount(Element parent,){
  super.mount(parent, newSlot); // 先将自己安装到树中
  _renderObject = widget.createRenderObject(this); // 创建对应的 RenderObject
  attachRenderObject(newSlot); // 将 RenderObject 添加到渲染树中。通过寻找祖先类中最近一个 RenderObjectElement，找到渲染树，然后插入其中
}

RenderObjectElement#update(Widget newWidget){ // RenderObjectElement 重写了 update，目的是为了更新 RenderObject
  super.update(newWidget); // 先更新 widget 配置
  widget.updateRenderObject(this, renderObject); // 更新 RenderObject 配置
}




调用 setState((){}) 的时候
_element.markNeedsBuild();
  _dirty = true; // 标记为脏，即需要重建
  - owner.scheduleBuildFor(this); =》 BuildOwner#scheduleBuildFor(Element){
    if (!_scheduledFlushDirtyElements && onBuildScheduled != null) { // 如果没有在绘制中，且已经有 onBuildScheduled 函数了，就调用它
      _scheduledFlushDirtyElements = true;
      onBuildScheduled();  // 实际调用的是 WidgetsBinding 中的 _handleBuildScheduled
        `- ensureVisualUpdate(); // 请求一个 frame`
    }
    `_dirtyElements.add(element);  // 将 Element 加入到脏数组，当 frame 到来时，可以重建，重绘制`
    element._inDirtyList = true; // 标记为在脏数组中
 }

当一个不活跃的 Element 重新加入到树中，也会调用 owner.scheduleBuildFor(this)

1. FrameCallback接收到一个 Vsync，进而调用 WidgetsBinding 中的 drawFrame()，传入根 Element 元素 renderViewElement，开始构建渲染树
2. 在 WidgetsBinding#drawFrame() 中，会遍历需要进行重建的 Element 数组 _dirtyElements，让其中的每个 Element 的 Element#rebuild()，让他们进行重建


rootElement -> childWidget.createElement() -> childElement.createRenderObject()


StatelessWidget 和 StatefulWidget 负责通过 build 接口 返回一个 RenderObjectWidget，


使用 Widget 描述（配置）Element，


如何判断 Widget 是否可以更新，相同

```
Widget# static bool canUpdate(Widget oldWidget, Widget newWidget) {
    return oldWidget.runtimeType == newWidget.runtimeType
        && oldWidget.key == newWidget.key;
  }
```


