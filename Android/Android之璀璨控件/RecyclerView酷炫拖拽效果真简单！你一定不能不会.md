


# 参考资料
[参考资料1](http://blog.csdn.net/liaoinstan/article/details/51200618)    
[参考资料2](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0630/3123.html)  
[参考资料3【推荐】](http://blog.csdn.net/yanzhenjie1003/article/details/51935982)
# 背景介绍
列表控件可以说是我们绝大部分App中都会使用的，为了提升交互乐趣，我们经常需要在列表中加入拖拽、滑动等操作，本篇我将介绍使用**ItemDragHelper**这个官方提供的交互帮助类来帮助**RecyclerView**实现这些复杂的交互。
# 走进ItemTouchHelper
要使用这个类，最关键的步骤是传给它一个**ItemTouchHelper.Callback**，其它的我们可以不用管。既然它是一个Callback，那么我们可以大概猜到，我们自然就是在它的一些回调函数里做操作了。下面我们就来看看我们需要在哪些回调函数里做操作。
先上个效果图：  
![效果图](http://upload-images.jianshu.io/upload_images/1869462-c725b7f8c6f239cd.gif?imageMogr2/auto-orient/strip)  


```
public class CustomItemTouchHelperCallback extends ItemTouchHelper.Callback {
  private OnItemTouchCallbackListener onItemTouchCallbackListener;

  private boolean canDrag = true;
  private boolean canSwipe = true;

  public CustomItemTouchHelperCallback(OnItemTouchCallbackListener onItemTouchCallbackListener) {
    this.onItemTouchCallbackListener = onItemTouchCallbackListener;
  }

  @Override
  public int getMovementFlags(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder) {
    RecyclerView.LayoutManager layoutManager = recyclerView.getLayoutManager();
    int dragFlags = 0;
    int swipeFlags = 0;
    if (layoutManager instanceof GridLayoutManager) {
      // 如果是Grid布局，则不能滑动，只能上下左右拖动
      dragFlags =
          ItemTouchHelper.UP | ItemTouchHelper.DOWN | ItemTouchHelper.LEFT | ItemTouchHelper.RIGHT;
      swipeFlags = 0;
    } else if (layoutManager instanceof LinearLayoutManager) {
      // 如果是纵向Linear布局，则能上下拖动，左右滑动
      if (((LinearLayoutManager) layoutManager).getOrientation() == LinearLayoutManager.VERTICAL) {
        dragFlags = ItemTouchHelper.UP | ItemTouchHelper.DOWN;
        swipeFlags = ItemTouchHelper.LEFT|ItemTouchHelper.RIGHT;
      } else {
        // 如果是横向Linear布局，则能左右拖动，上下滑动
        swipeFlags = ItemTouchHelper.UP | ItemTouchHelper.DOWN;
        dragFlags = ItemTouchHelper.LEFT|ItemTouchHelper.RIGHT;
      }
    }
    return makeMovementFlags(dragFlags, swipeFlags); //该方法指定可进行的操作
  }

  /**
   * 拖动时回调，在这里处理拖动事件
   * 
   * @param viewHolder 被拖动的view
   * @param target 目标位置的view
   */
  @Override
  public boolean onMove(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder,
      RecyclerView.ViewHolder target) {
    //这里我们直接将参数传递到回调接口方法中，提高复用性
    return onItemTouchCallbackListener.onMove(recyclerView, viewHolder, target);
  }

  /**
   * 滑动时回调
   * 
   * @param direction 回调方向
   */
  @Override
  public void onSwiped(RecyclerView.ViewHolder viewHolder, int direction) {
    onItemTouchCallbackListener.onSwiped(viewHolder, direction);
  }

  /**
   * 在这个回调中，如果返回true，表示可以触发长按拖动事件，false则表示不能
   */
  @Override
  public boolean isLongPressDragEnabled() {
    return canDrag;
  }

  /**
   * 在这个回调中，如果返回true，表示可以触发滑动事件，false表示不能
   */
  @Override
  public boolean isItemViewSwipeEnabled() {
    return canSwipe;
  }

  public void setCanDrag(boolean canDrag) {
    this.canDrag = canDrag;
  }

  public void setCanSwipe(boolean canSwipe) {
    this.canSwipe = canSwipe;
  }
}

```
下面我们看看怎么来使用这个我们自定义的Callback。
```
public class DraggerRecyclerViewActivity extends BaseActivity {
  @BindView(R.id.rv_dragger_list)
  RecyclerView draggerList;

  private List<String> datas = new ArrayList<>();
  private TextTestAdapter adapter;
  private CustomItemTouchHelper itemTouchHelper;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_dragger_recycler_view);
    ButterKnife.bind(this);
    initData();
    initView();
    addListener();
  }

  @Override
  protected void initData() {
    for (int i = 0; i < 100; i++) {
      datas.add("我是Item" + i);
    }
  }

  @Override
  protected void initView() {
    draggerList.setItemAnimator(new DefaultItemAnimator());
    draggerList.addItemDecoration(new RecyclerView.ItemDecoration() {
      @Override
      public void getItemOffsets(Rect outRect, int itemPosition, RecyclerView parent) {
        if (itemPosition != parent.getChildCount() - 1) {
          outRect.bottom = (int) DisplayUtils.dipToPx(0.5f);
        }
      }
    });
    draggerList.setLayoutManager(new LinearLayoutManager(this));
    adapter = new TextTestAdapter(this, datas);
    //创建ItemTouchHelper
    itemTouchHelper = new CustomItemTouchHelper(new OnItemTouchCallbackListener() {
      @Override
      public boolean onMove(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder,
          RecyclerView.ViewHolder target) {
        if (datas == null) {
          return false;
        }
        //处理拖动排序
        //使用Collection对数组进行重排序，目的是把我们拖动的Item换到下一个目标Item的位置
        Collections.swap(datas, viewHolder.getAdapterPosition(), target.getAdapterPosition());
        //通知Adapter它的Item发生了移动
        adapter.notifyItemMoved(viewHolder.getAdapterPosition(), target.getAdapterPosition());
        return true;
      }

      @Override
      public void onSwiped(RecyclerView.ViewHolder viewHolder, int direction) {
        if (datas == null) {
          return;
        }
        //处理滑动删除
        //直接从数据中删除该Item的数据
        datas.remove(viewHolder.getAdapterPosition());
        //通知Adapter有Item被移除了
        adapter.notifyItemRemoved(viewHolder.getAdapterPosition());
      }
    });
    //并绑定RecyclerView
    itemTouchHelper.attachToRecyclerView(draggerList);
    draggerList.setAdapter(adapter);
  }

  @Override
  protected void addListener() {

  }
}
```
下面是**ItemTouchHelper**。
```
public class CustomItemTouchHelper extends ItemTouchHelper {

  public CustomItemTouchHelper(OnItemTouchCallbackListener onItemTouchCallbackListener) {
    //直接把回调交给父类处理就好
    super(new CustomItemTouchHelperCallback(onItemTouchCallbackListener));
  }
}
```
很简单是不是？把它自定义出来其实就是为了防止后期还要添加别的操作，如果不需要，可以直接用API中的ItemTouchHelper。

# 总结
归根结底，使用**ItemTouchHelper**来实现**RecyclerView**的拖拽、滑动操作，最主要的还是重写**ItemTouchHelper.Callback**，在这个回调中，比较重要的方法是以下几个：
- getMovementFlags(): 这个方法中需要返回可进行的操作Flags，这些Flags需要调用下面的方法合成；
- makeMovementFlags()：这个方法将drag和move操作的Flags合成，作为**getMovementFlags**中的返回项；
- onMove()：发生拖动时调用；
- onSwiped()：发生滑动时调用；
- isLongPressDragEnabled()：是否能触发长按拖动；
- isItemViewSwipeEnabled()：是否能触发滑动；
# 探讨
最近看到一个观点，说美国犯罪率的下降应该大部分归功于堕胎合法化的政策，而不是别的因素。因为堕胎使很多底层人民，他们意外怀孕，却无力好好抚养孩子，提供良好的生存环境给他们，这些孩子在未来是最有可能从事犯罪的。而堕胎的合法化使这些孩子一部分未能来到这个世界上，所以从根源上导致了犯罪率的下降。  
这事儿，你怎么看？

如果这篇文章对你有帮助的话，记得点个赞，关注走一波哦！
