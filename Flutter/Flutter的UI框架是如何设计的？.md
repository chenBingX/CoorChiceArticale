GUI（Graphical User Interface）基本要素：
- 事件：来自系统、用户输入的事件
- 窗口：负责视图展示窗口的建立、销毁，视图内容的管理
- 渲染：负责绘制渲染窗口内容







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



初始化：
1. GestureBinding: {
    window.onPointerDataPacket = _handlePointerDataPacket;
}

2.



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