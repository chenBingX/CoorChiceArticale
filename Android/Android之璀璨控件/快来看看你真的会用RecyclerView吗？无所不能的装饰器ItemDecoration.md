
# 参考资料
[参考资料1](http://blog.piasy.com/2016/03/26/Insight-Android-RecyclerView-ItemDecoration/)；
# 背景介绍
*RecyclerView*由于它强大的灵活性，已经可以代替掉传统的*ListView*和*GridView*等列表控件了。但是也因为它的灵活性，一些东西就没有固定，需要我们自己来实现，比如*RecyclerView*就没有提供默认的分割线，当我们需要一条分割线时，我们需要自己通过继承*RecyclerView.ItemDecoration*来实现。当然，它不仅仅可以实现分割线，还能创造很多不一样的效果，这取决于我们的想象力在这个限制范围内能到什么程度了。本篇我们来好好聊聊*ItemDecoration*。
# 认识ReccylerView.ItemDecoration
## 为RecyclverView添加ItemDecoration
我们可以通过调用：
```
mRecyclerView.addItemDecoration(mItemDecoration)；
```
来为我们的*RecyclerView*添加一个自定义的*ItemDecoration*装饰它。  
这里值得注意的是我们采用的是**addXXX**方法，这意味着我们可以为*RcyclerView*添加多个*ItemDecoration*。打开该方法的源码我们可以看到：
```
//这是我们通常调用的代码
public void addItemDecoration(ItemDecoration decor) {
        //这里又调用了另一个重载的方法
        addItemDecoration(decor, -1);
    }
    
/**
     * Add an {@link ItemDecoration} to this RecyclerView. Item decorations can
     * affect both measurement and drawing of individual item views.
     *
     * <p>Item decorations are ordered. Decorations placed earlier in the list will
     * be run/queried/drawn first for their effects on item views. Padding added to views
     * will be nested; a padding added by an earlier decoration will mean further
     * item decorations in the list will be asked to draw/pad within the previous decoration's
     * given area.</p>
     *
     * @param decor Decoration to add
     * @param index Position in the decoration chain to insert this decoration at. If this value
     *              is negative the decoration will be added at the end.
     * 通过文档我们可以知道，index参数控制这我们add的ItemDecoration添加到mItemDecorations数组中的位置。如果小于0，我们的ItemDecoration将被添加到最后。
     */
public void addItemDecoration(ItemDecoration decor, int index) {
        if (mLayout != null) {
            mLayout.assertNotInLayoutOrScroll("Cannot add item decoration during a scroll  or"
                    + " layout");
        }
        if (mItemDecorations.isEmpty()) {
            setWillNotDraw(false);
            //注意，当我们首次添加ItemDecoration时，mItemDecorations是一个空的数组，
           //但这事我们就需要让View开启自我绘制，否则RecyclerView的onDraw()方法将有可能不被执行。
           //我们在写自定义ViewGroup时也需要注意这个问题。
        }
        if (index < 0) {
        //这个逻辑保证了小于0，添加到末尾
            mItemDecorations.add(decor);
        } else {
        //添加到指定位置
            mItemDecorations.add(index, decor);
        }
        //这个方法最终把RecyclerView的Item的LayoutParams的mInsertDirty属性设置为true，
        //这样在measure时，才能够把所有的ItemDecoration中的itemOffset添加到Item的布局参数上。
        markItemDecorInsetsDirty();
        requestLayout();
    }
```
所以，我们可以多次调用`addItemDecoration()`，并且每次添加的*ItemDecoration*的*itemOffset*都将累加到Item的布局参数上。
## 自定义ItemDecoration
对于自定义*ItemDecoration*我们通常仅仅关心以下3个方法就行。
### getItemOffsets()
这个方法有2个重载方法：
```
public void getItemOffsets(Rect outRect, int itemPosition, RecyclerView parent) {
            outRect.set(0, 0, 0, 0);
            //注意，重写时super要慎重使用，否则设置的Rect会被重置。
            //通过上面的讲解，我们知道，在这里设置的outRect的参数最终都会被累加到Item的布局参数上。
            //如果值设为负数，那么Item会发生重叠。
        }

//这个方法主要能够方便的获取到View和当前RecyclerView的状态，
//但它最终调用的仍然是上一个方法，所以重写这个方法需要注意super，最好就不要要。
public void getItemOffsets(Rect outRect, View view, RecyclerView parent, State state) {
            getItemOffsets(outRect, ((LayoutParams) view.getLayoutParams()).getViewLayoutPosition(),
                    parent);
        }
```
这里借张图，侵删！
![设置不同outRect参数的效果图](http://upload-images.jianshu.io/upload_images/1869462-da4ba78edaec2c94.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### onDraw()
该方法同样有两个重载方法：
```
public void onDraw(Canvas c, RecyclerView parent){
    //在这个方法中利用Canvas绘制点什么，
    //它最终会绘制在Item的下一层，如果它超出Item，那么超出部分将可见。
    //注意，计算Item的top时，记得加上Translation，否则不能准确计算
}

//该方法最终还是调用上一个方法，所以要注意super，最好就不要。这个方法能够获取到RecyclerView状态。
public void onDraw(Canvas c, RecyclerView parent, State state) {
            onDraw(c, parent);
        }
```
借张图，侵删！
![效果图](http://upload-images.jianshu.io/upload_images/1869462-f4738c4b750b1464.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
这里有个坑，在我使用`mDrawable.canvas()`来绘制分割线的时候，由于`mDrawable.getIntrinscXXX()`获得的值为-1，所以导致了绘制效果不可见。解决办法是在计算*bottom*或*right*时，直接指定我们所希望的间距，除非我们已经设置mDrawable的尺寸。  
 **还有一点需要注意：最终getItemOffset()方法中设置的偏移距离会叠加到Item的布局尺寸参数上，而分割线的绘制应该在Item底部(去尾)，或者说Item顶部(去头)，绘制区域**

### onDrawOver()
与`onDraw()`方法类似，不同的是它将被覆盖在Item上面。它同样有两个重载方法:
```
public void onDrawOver(Canvas c, RecyclerView parent, State state) {
            onDrawOver(c, parent);
        }


@Deprecated
public void onDrawOver(Canvas c, RecyclerView parent) {
        }
```
# 总结
通过自定义`ItemDecoration`我们可以实现需要吊炸天的效果。先遵守规则，然后天马行空。
# 探讨
今天在换燕x矿泉水时(这不是广告)，发现上面裹桶口裹的一层保护塑料，裹的倒是十分结实！但是我找了半天没发现有可以下手撕开的地方，也就是说要么硬抠开，要么借助工具，总之就是不能让你轻易就打开。不知道生产这种桶装水的人自己用不用，或者说自己换过水没？  
这事儿，你怎么看？


如果这篇文章对你有帮助的话，记得点个赞哦！想要交流交流的话可以加个关注哦！
