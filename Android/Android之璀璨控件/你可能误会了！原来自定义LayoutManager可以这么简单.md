

# 参考资料
[参考资料1](http://blog.csdn.net/qibin0506/article/details/52676670);  
[参考资料2](http://blog.csdn.net/huachao1001/article/details/51594004#rd)；  
[参考资料3](https://chacojack.github.io/2016/09/14/%E6%8A%8ARecyclerView%E5%81%9A%E6%88%90-%E9%A9%AC-%E8%9C%82-%E7%AA%9D/)；  
[参考资料4](http://www.jianshu.com/p/7bb7556bbe10)；  
# 背景介绍
**RecyclerView**由于其强大的扩展性，现在已经逐步的取代了**ListView**和**GridView**了。为了实现不同的布局效果，我们会用到官方提供的**LinearLayoutManager**、**GridLayoutManager**和**StaggeredGridLayoutManager**。但这些布局只能满足日常需求，在一些比较复杂的布局中，它们就力不从心了，强行拼凑实现，带来的后果就是较差的体验和性能。所以能够自定义**LayoutManager**还是十分必要的，它能够解放创造力，构造复杂的、流畅的滑动列表。上面几篇参考资料中就实现了一些不寻常的效果，我们可以看到，这些效果如果用常规的方案去实现将会十分蹩脚。

# 揭开LayoutManager中不为人知的秘密
自定义`LayoutManager`主要要求我们完成三件事情：
- 计算每个ItemView的位置；
- 处理滑动事件；
- 缓存并重用ItemView；  

而我们比较重要的工作是在`onLayoutChildern() `   这个回调方法中完成的。

下面我们就来一一解析。
## 预先准备
当我们**extends RecyclerView.LayoutManager**是，我们会被强制要求重写`generateDefaultLayoutParams()`方法，如方法名字一样，我们需要提供一个默认的*LayoutParams*，这里为我们的每个`ItemView`提供默认的`LayoutParams`，所以它能够直接影响到我们的布局效果，这里我们设置成`WRAP_CONTENT`，让`ItemView`获得决定权。
```
@Override
  public RecyclerView.LayoutParams generateDefaultLayoutParams() {
    return new RecyclerView.LayoutParams(RecyclerView.LayoutParams.WRAP_CONTENT,
        RecyclerView.LayoutParams.WRAP_CONTENT);
  }
```
## 计算ItemView的位置
### 1.实现简单的LayoutManager
先看效果图:  
![简单LayoutManager](http://upload-images.jianshu.io/upload_images/1869462-a00b3599f44d2dd1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
再看代码：
```
@Override
  public void onLayoutChildren(RecyclerView.Recycler recycler, RecyclerView.State state) {
    super.onLayoutChildren(recycler, state);
    // 先把所有的View先从RecyclerView中detach掉，然后标记为"Scrap"状态，表示这些View处于可被重用状态(非显示中)。
    // 实际就是把View放到了Recycler中的一个集合中。
    detachAndScrapAttachedViews(recycler);
    calculateChildrenSite(recycler);
  }

  private void calculateChildrenSite(RecyclerView.Recycler recycler) {
    totalHeight = 0;
    for (int i = 0; i < getItemCount(); i++) {
      // 遍历Recycler中保存的View取出来
      View view = recycler.getViewForPosition(i);
      addView(view); // 因为刚刚进行了detach操作，所以现在可以重新添加
      measureChildWithMargins(view, 0, 0); // 通知测量view的margin值
      int width = getDecoratedMeasuredWidth(view); // 计算view实际大小，包括了ItemDecorator中设置的偏移量。
      int height = getDecoratedMeasuredHeight(view);
      
      Rect mTmpRect = new Rect();
      //调用这个方法能够调整ItemView的大小，以除去ItemDecorator。
      calculateItemDecorationsForChild(view, mTmpRect);
      
      // 调用这句我们指定了该View的显示区域，并将View显示上去，此时所有区域都用于显示View，
      //包括ItemDecorator设置的距离。
      layoutDecorated(view, 0, totalHeight, width, totalHeight + height);
      totalHeight += height;
    }
  }

```
这段代码逻辑简单，它实现的其实就是一个简单的垂直线性布局，当然现在还不能滑动，也没有缓存机制。在这段代码中，我们先调用`detachAndScrapAttachedViews(recycler);`将所有的ItemView标记为**Scrap**状态，然后在挨个取出来，计算他们应该布局到什么位置，并用成员变量*totalHeight*记录总高度，最后依次调用`layoutDecorated()`将ItemView布局上去。
### 2.两列式的LayoutManager
先看效果图：  
![效果图](http://upload-images.jianshu.io/upload_images/1869462-dd9baf25eea4b54b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
有了上例的基础，我们只需要稍作调整，直接看下面代码，注意注释部分。
```
private void calculateChildrenSite(RecyclerView.Recycler recycler) {
    totalHeight = 0;
    for (int i = 0; i < getItemCount(); i++) {
      View view = recycler.getViewForPosition(i);
      addView(view);
      //我们自己指定ItemView的尺寸。
      measureChildWithMargins(view, DisplayUtils.getScreenWidth() / 2, 0); 
      int width = getDecoratedMeasuredWidth(view);
      int height = getDecoratedMeasuredHeight(view);
      Rect mTmpRect = new Rect();
      calculateItemDecorationsForChild(view, mTmpRect);
      if (i % 2 == 0) { //当i能被2整除时，是左，否则是右。
        //左
        layoutDecoratedWithMargins(view, 0, totalHeight, DisplayUtils.getScreenWidth() / 2,
            totalHeight + height);
      } else {
        //右，需要换行
        layoutDecoratedWithMargins(view, DisplayUtils.getScreenWidth() / 2, totalHeight,
            DisplayUtils.getScreenWidth(), totalHeight + height);
        totalHeight = totalHeight + height;
        LogUtils.e(i + "->" + totalHeight);
      }
    }
  }
```
## 处理滑动
先来看一下效果:  
![效果图](http://upload-images.jianshu.io/upload_images/1869462-792b46f64676d34c.gif?imageMogr2/auto-orient/strip)

滑动事件主要涉及到4个方法需要重写，我们直接来看代码：
```
@Override
  public boolean canScrollVertically() {
    //返回true表示可以纵向滑动
    return true;
  }

  @Override
  public int scrollVerticallyBy(int dy, RecyclerView.Recycler recycler, RecyclerView.State state) {
    //列表向下滚动dy为正，列表向上滚动dy为负，这点与Android坐标系保持一致。
    //实际要滑动的距离
    int travel = dy;

    LogUtils.e("dy = " + dy);
    //如果滑动到最顶部
    if (verticalScrollOffset + dy < 0) {
      travel = -verticalScrollOffset;
    } else if (verticalScrollOffset + dy > totalHeight - getVerticalSpace()) {//如果滑动到最底部
      travel = totalHeight - getVerticalSpace() - verticalScrollOffset;
    }

    //将竖直方向的偏移量+travel
    verticalScrollOffset += travel;

    // 调用该方法通知view在y方向上移动指定距离
    offsetChildrenVertical(-travel);

    return travel;
  }

  private int getVerticalSpace() {
    //计算RecyclerView的可用高度，除去上下Padding值
    return getHeight() - getPaddingBottom() - getPaddingTop();
  }

  @Override
  public boolean canScrollHorizontally() {
    //返回true表示可以横向滑动
    return super.canScrollHorizontally();
  }

  @Override
  public int scrollHorizontallyBy(int dx, RecyclerView.Recycler recycler, RecyclerView.State state) {
    //在这个方法中处理水平滑动
    return super.scrollHorizontallyBy(dx, recycler, state);
  }
```
## 缓存并重用ItemView
在上面代码的基础上我们稍作改动，加入缓存，先看下面的log信息，它显示虽然有100个Item，但childCount稳定在26：  
![log](http://upload-images.jianshu.io/upload_images/1869462-461ac4f8c702a46d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
下面来看看代码的变化，我展示了完整的代码，留心注释。
```
public class CustomLayoutManager extends RecyclerView.LayoutManager {
  /** 用于保存item的位置信息 */
  private SparseArray<Rect> allItemRects = new SparseArray<>();
  /** 用于保存item是否处于可见状态的信息 */
  private SparseBooleanArray itemStates = new SparseBooleanArray();

  public int totalHeight = 0;
  private int verticalScrollOffset;

  @Override
  public RecyclerView.LayoutParams generateDefaultLayoutParams() {
    return new RecyclerView.LayoutParams(ViewGroup.LayoutParams.WRAP_CONTENT,
        ViewGroup.LayoutParams.WRAP_CONTENT);
  }

  @Override
  public void onLayoutChildren(RecyclerView.Recycler recycler, RecyclerView.State state) {
    if (getItemCount() <= 0 || state.isPreLayout()) {
      return;
    }
    super.onLayoutChildren(recycler, state);
    detachAndScrapAttachedViews(recycler);
    /* 这个方法主要用于计算并保存每个ItemView的位置 */
    calculateChildrenSite(recycler);
    recycleAndFillView(recycler, state);
  }

  private void calculateChildrenSite(RecyclerView.Recycler recycler) {
    totalHeight = 0;
    for (int i = 0; i < getItemCount(); i++) {
      View view = recycler.getViewForPosition(i);
      addView(view);
      // 我们自己指定ItemView的尺寸。
      measureChildWithMargins(view, DisplayUtils.getScreenWidth() / 2, 0);
      calculateItemDecorationsForChild(view, new Rect());
      int width = getDecoratedMeasuredWidth(view);
      int height = getDecoratedMeasuredHeight(view);

      Rect mTmpRect = allItemRects.get(i);
      if (mTmpRect == null) {
        mTmpRect = new Rect();
      }

      if (i % 2 == 0) { // 当i能被2整除时，是左，否则是右。
        // 左
        mTmpRect.set(0, totalHeight, DisplayUtils.getScreenWidth() / 2, totalHeight + height);
      } else {
        // 右，需要换行
        mTmpRect.set(DisplayUtils.getScreenWidth() / 2, totalHeight, DisplayUtils.getScreenWidth(),
            totalHeight + height);
        totalHeight = totalHeight + height;
      }

      // 保存ItemView的位置信息
      allItemRects.put(i, mTmpRect);
      // 由于之前调用过detachAndScrapAttachedViews(recycler)，所以此时item都是不可见的
      itemStates.put(i, false);
    }
  }


  private void recycleAndFillView(RecyclerView.Recycler recycler, RecyclerView.State state) {
    if (getItemCount() <= 0 || state.isPreLayout()) {
      return;
    }

    // 当前scroll offset状态下的显示区域
    Rect displayRect= new Rect(0, verticalScrollOffset, getHorizontalSpace(),
        verticalScrollOffset + getVerticalSpace());

    /**
     * 将滑出屏幕的Items回收到Recycle缓存中
     */
    Rect childRect = new Rect();
    for (int i = 0; i < getChildCount(); i++) {
      //这个方法获取的是RecyclerView中的View，注意区别Recycler中的View
      //这获取的是实际的View
      View child = getChildAt(i);
      //下面几个方法能够获取每个View占用的空间的位置信息，包括ItemDecorator
      childRect.left = getDecoratedLeft(child);
      childRect.top = getDecoratedTop(child);
      childRect.right = getDecoratedRight(child);
      childRect.bottom = getDecoratedBottom(child);
      //如果Item没有在显示区域，就说明需要回收
      if (!Rect.intersects(displayRect, childRect)) {
        //移除并回收掉滑出屏幕的View
        removeAndRecycleView(child, recycler);
        itemStates.put(i, false); //更新该View的状态为未依附
      }
    }

    //重新显示需要出现在屏幕的子View
    for (int i = 0; i < getItemCount(); i++) {
      //判断ItemView的位置和当前显示区域是否重合
      if (Rect.intersects(displayRect, allItemRects.get(i))) {
        //获得Recycler中缓存的View
        View itemView = recycler.getViewForPosition(i);
        measureChildWithMargins(itemView, DisplayUtils.getScreenWidth() / 2, 0);
        //添加View到RecyclerView上
        addView(itemView);
        //取出先前存好的ItemView的位置矩形
        Rect rect = allItemRects.get(i);
        //将这个item布局出来
        layoutDecoratedWithMargins(itemView,
          rect.left,
          rect.top - verticalScrollOffset,  //因为现在是复用View，所以想要显示在
          rect.right,
          rect.bottom - verticalScrollOffset);
        itemStates.put(i, true); //更新该View的状态为依附
      }
    }
    LogUtils.e("itemCount = " + getChildCount());
  }


  @Override
  public boolean canScrollVertically() {
    // 返回true表示可以纵向滑动
    return true;
  }

  @Override
  public int scrollVerticallyBy(int dy, RecyclerView.Recycler recycler, RecyclerView.State state) {
    //每次滑动时先释放掉所有的View，因为后面调用recycleAndFillView()时会重新addView()。
    detachAndScrapAttachedViews(recycler);
    // 列表向下滚动dy为正，列表向上滚动dy为负，这点与Android坐标系保持一致。
    // 实际要滑动的距离
    int travel = dy;

    LogUtils.e("dy = " + dy);
    // 如果滑动到最顶部
    if (verticalScrollOffset + dy < 0) {
      travel = -verticalScrollOffset;
    } else if (verticalScrollOffset + dy > totalHeight - getVerticalSpace()) {// 如果滑动到最底部
      travel = totalHeight - getVerticalSpace() - verticalScrollOffset;
    }
    // 调用该方法通知view在y方向上移动指定距离
    offsetChildrenVertical(-travel);
    recycleAndFillView(recycler, state); //回收并显示View
    // 将竖直方向的偏移量+travel
    verticalScrollOffset += travel;
    return travel;
  }

  private int getVerticalSpace() {
    // 计算RecyclerView的可用高度，除去上下Padding值
    return getHeight() - getPaddingBottom() - getPaddingTop();
  }

  @Override
  public boolean canScrollHorizontally() {
    // 返回true表示可以横向滑动
    return super.canScrollHorizontally();
  }

  @Override
  public int scrollHorizontallyBy(int dx, RecyclerView.Recycler recycler,
      RecyclerView.State state) {
    // 在这个方法中处理水平滑动
    return super.scrollHorizontallyBy(dx, recycler, state);
  }

  public int getHorizontalSpace() {
    return getWidth() - getPaddingLeft() - getPaddingRight();
  }
}
```
实现缓存最主要的就是先把**每个ItemView的位置信息保存起来**，然后在滑动过程中通过**判断每个ItemView的位置是否和当前RecyclerView应该显示的区域有重合，若有就显示它，若没有就移除并回收**。  
# 总结
实现自己的自定义**LayoutManager**主要的三个步骤：
- 计算每个ItemView的位置；
- 添加滑动事件；
- 实现缓存。  

我们需根据代码多理解，多思考，然后动手写属于自己的**LayoutManager**。
# 探讨
最近路上留意到很多三轮摩托老司机开车十分的奔放，和拉力赛有得一拼。之前坐过几次，坐的时候因为赶时间，所以当时感觉老司机好负责。但最近作为路人看，老司机开车开的太危险，强行抢道，疯狂按喇叭...整个是横冲直撞的态势。总之觉得很危险。  
这件事，你怎么看？

如果你觉得这篇文章对你有帮助的话，点赞走一走，再加个关注，互相交流下。 
