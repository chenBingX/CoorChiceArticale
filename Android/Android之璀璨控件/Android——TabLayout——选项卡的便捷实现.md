> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

`TabLayout`可以方便的实现导航标签功能，它需要与`ViewPager`联合使用，`ViewPager`加载适配器，`TabLayout`加载`ViewPager`。  
使用前需要先导入**desgin包**。
# 属性
```
app:tabIndicatorColor="@color/white"      // 下方滚动的下划线颜色  
app:tabSelectedTextColor="@color/gray"    // tab被选中后，文字的颜色  
app:tabTextColor="@color/white"           // tab默认的文字颜色  
```

# 几个关键方法
```
mTabLayout.setTabMode(TabLayout.MODE_FIXED); //设置TabLayout的标签模式

mTabLayout.addTab(); //为TabLayout添加标签

mTabLayout.setupWithViewPager(); //为TabLayout添加ViewPager
```

# 下面在实例中介绍
```
activity.xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    tools:context="com.icechen.tablayoutdemo.MainActivity">
    <android.support.design.widget.TabLayout
        android:id="@+id/tabLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:tabTextColor="#252525"
        app:tabSelectedTextColor="#ff7473"
        app:tabIndicatorColor="#d09ec6"
        >
    </android.support.design.widget.TabLayout>
    <android.support.v4.view.ViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1">
    </android.support.v4.view.ViewPager>
</LinearLayout>


activity.java

public class MainActivity extends AppCompatActivity {

    private TabLayout mTabLayout;
    private ViewPager mViewPager;
    private String[] titles = {"最新","一周最热","买买买","读书","设计","文艺","礼物","指南","爱美"};
    private FragmentManager fragmentManager;
    private List<Fragment> fragments = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        fragmentManager = getSupportFragmentManager();
        for (int i = 0; i < titles.length; i++) {
            fragments.add(Fragment1.newInstance(""+i));
        }
        initView();
    }

    private void initView() {
        mTabLayout = (TabLayout) findViewById(R.id.tabLayout);
        mViewPager = (ViewPager) findViewById(R.id.viewPager);

        for (int i = 0; i < titles.length; i++) {
            //设置tab的标题
            mTabLayout.addTab(mTabLayout.newTab().setText(titles[i]));
        }

        mViewPager.setAdapter(new FragmentPagerAdapter(fragmentManager) {
            @Override
            public Fragment getItem(int position) {
                return fragments.get(position);
            }

            @Override
            public int getCount() {
                return fragments.size();
            }

            //使用Fragment需要指定好tab
            @Override
            public CharSequence getPageTitle(int position) {
                return titles[position % titles.length];
            }
        });

        //MODE_SCROLLABLE:可以滚动
        //MODE_FIXED:会适配到屏幕
        mTabLayout.setTabMode(TabLayout.MODE_SCROLLABLE);
        //将ViewPager加载到TabLayout中，使它们能够联动
        mTabLayout.setupWithViewPager(mViewPager);
    }
}


fragment.java

public class Fragment1 extends Fragment {

    public static Fragment1 newInstance(String content) {
        Bundle args = new Bundle();
        args.putString("content",content);
        Fragment1 fragment = new Fragment1();
        fragment.setArguments(args);
        return fragment;
    }

    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        return inflater.inflate(R.layout.fragment_tab,container,false);
    }

    @Override
    public void onActivityCreated(@Nullable Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);
        ((TextView)(getView().findViewById(R.id.content))).setText(getArguments().getString("content"));
    }
}
```

![效果](http://upload-images.jianshu.io/upload_images/1869462-a6fdf183633196fe?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-2d999c7e11786f76.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
