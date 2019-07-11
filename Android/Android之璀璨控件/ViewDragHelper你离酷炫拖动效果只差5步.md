
# 使用ViewDragHelper需要5个步骤
## 一、使用静态工厂法实例化一个ViewDragHelper对象
```
/**
     * 该方法用于实例化ViewDragHelper对象
     */
    private void init() {
        mViewDragHelper = ViewDragHelper.create(this, callback);  //实例化ViewDragHelper对象
    }
```
## 二、创建上面使用的callback参数
**该参数是ViewDragHelper.Callback类型的，这里需要通过匿名内部类来重写至少以下4个方法**
 ```
private ViewDragHelper.Callback callback = new ViewDragHelper.Callback() {
        /**
         * 该方法用于捕获ViewGroup中需要被移动的View。
         * @param child
         * @param pointerId
         * @return 使用判断来返回需要被ViewDragHelper的子view是哪一个
         */
        @Override
      1.  public boolean tryCaptureView(View child, int pointerId) {
            showLog("tryCaptureView()");
            return mView0 == child;  //捕获需要拖动的view
        }

        @Override
      2.  public int clampViewPositionVertical(View child, int top, int dy) {
            showLog("clampViewPositionVertical()");
            return 0;
        }

        /**
         * @param child
         * @param left  表示系统认为的水平方向需要移动多少
         * @param dx 表示水平方向上移动了多少距离
         * @return 返回left则跟着手指在水平方向上移动
         */
        @Override
      3.  public int clampViewPositionHorizontal(View child, int left, int dx) {
            showLog("left = " + left);
            return left;
        }

        /**
         * 该方法会在手指抬起后回调，决定了view的移动。
         * @param releasedChild
         * @param xvel 表示水平方向的移动速度
         * @param yvel 表示垂直方向的移动速度
         */
        @Override
      4.  public void onViewReleased(View releasedChild, float xvel, float yvel) {
            showLog("onViewReleased()");
            super.onViewReleased(releasedChild, xvel, yvel);
            if (mView0.getLeft()<500){
                //距离不足，关闭菜单
                mViewDragHelper.smoothSlideViewTo(mView0,0,0); //丝滑的滑动view到（0，0）位置
                ViewCompat.postInvalidateOnAnimation(DragHelperView.this); //重绘viewGroup
            } else {
                //距离足够，打开菜单
                mViewDragHelper.smoothSlideViewTo(mView0,300,0);
                ViewCompat.postInvalidateOnAnimation(DragHelperView.this);
            }
        }
    };
```
## 三、获得要操作的View及view的尺寸
```
    /**
     * 该方法在填充布局后调用，用于获取到布局中的子view。
     */
    @Override
    protected void onFinishInflate() {
        super.onFinishInflate();
        mView0 = getChildAt(0);
        mView1 = getChildAt(1);
    }

    /**
     * 该方法是为了获得view的尺寸
     * @param w
     * @param h
     * @param oldw
     * @param oldh
     */
    @Override
    protected void onSizeChanged(int w, int h, int oldw, int oldh) {
        super.onSizeChanged(w, h, oldw, oldh);
        mWidth = mView0.getMeasuredWidth(); //获得view的宽
    }
```

## 四、重写`computeScroll()`方法来实现滑动
**这有点像Scroller的使用**
```
    @Override
    public void computeScroll() {
        super.computeScroll();
        if (mViewDragHelper.continueSettling(true)){ //判断view是否在继续移动
            ViewCompat.postInvalidateOnAnimation(this);  //是，刷新重绘viewGroup
        }
    }
```
## 五、拦截触摸事件，并且交给ViewDragHelper对象处理
```
    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {

        return mViewDragHelper.shouldInterceptTouchEvent(ev); //拦截处理触摸事件，并传递给ViewDragHelper
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        mViewDragHelper.processTouchEvent(event);  //将触摸事件传递给ViewDragHelper处理
        return true;
    }
```

如果你觉得还不错，那就动动小捶捶敲个关注、点个赞哦😘！
