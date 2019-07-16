# 从头开始看xml解析

# 1. xml文件的编译和解析
## 1.1 xml的编译
在编译器编译期间，编译器会通过aapt(Android Asset Pakaging Tool，安卓资源打包工具)工具把xml文件转化为二进制文件，然后打包到资源索引文件 `resources.arsc` 中，这个文件记录了每一个资源的名称、类型、值和配置信息。  

### 1.1.1 **aapt是如何把xml文件转化为二进制文件的呢？**  

aapt会读取xml文件，然后将xml文件解析成一个 `ResXMLTree` 对象，然后把这个对象进行序列化，即将xml文件转化为了二进制格式了。这个解析过程是使用c++编写的，效率较高一些。  

### 1.1.2 **为什么需要把xml文件编译成二进制文件呢？**
二进制的xml文件使用整数来描述，没有了解析字符串的过程，可直接读取标签属性，所以效率更高。  

同时，相比直接储存字符串形式的xml文件，二进制的xml文件会占用更小的内存。

### 1.1.3 **运行时如何访问 `resource.arsc` 中的内容呢？**
在程序运行期间，Android提供了应用全局的 `Resources` 对象和 `AssetsManager` 对象来访问 `resources.arsc` 中的资源。    

 `AssetsManager` 可以通过文件名来直接访问被编译过的和没被编译过的资源。  
 
 `Resources` 可以通过资源Id来访问哪些被编译过的资源。事实上，`Resources` 是先通过资源Id查找到对应的文件名，然后再通过 `AssetsManager` 获取到对应资源。  
 
 
 ![](https://gw.alicdn.com/tfs/TB1HfQAjwvD8KJjy0FlXXagBFXa-486-441.jpg)



### 1.1.4 aapt简要介绍

[参考资料](http://www.jianshu.com/p/8d691b6bf8b4)

![](https://gw.alicdn.com/tfs/TB1DIcHjv6H8KJjy0FjXXaXepXa-580-562.jpg)

  
  
aapt是Android开发套件提供的一个资源编译打包工具，在 `[sdk path]/build-tools/[版本号]/` 下可以找到。  

该工具可以查看、创建、更新文档附件（包括zip、jar、apk），还可以将资源文件编译为二进制格式，就如上面所说。  

aapt会将 `AndroidManifest.xml`、`res/` 及 `assets/` 包下的资源进行编译打包。其中：  


- `assets/` 和 `res/raw/` 下的资源会被原封不动的打入apk中。  


- 除了 `assets/` 下的资源外，其它资源都会被编译处理，并且生成 `resources.arsc` 文件，以及对应的资源ID常量类`R.java` 。  

## 1.2 xml的解析

通常，我们对这句代码都比较熟悉：  

```
LayoutInflater.from(this).inflate(R.layout.item_test, container, true);
```

通过上面这句代码，我们就可以获得一个提前编写好的xml对应的View实例。其背后实际是通过layoutid找到了对应的xml资源文件，然后解析该xml，然后再创建对应的View实例。下面就看看这个过程具体是怎么实现的。

### 1.2.1 获取LayoutInflater

`LayoutInflater.java`  


```
public static LayoutInflater from(Context context) {
        LayoutInflater LayoutInflater =
                (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        if (LayoutInflater == null) {
            throw new AssertionError("LayoutInflater not found.");
        }
        return LayoutInflater;
    }
```  
这就是 `LayoutInflater.from(context)` 背后的秘密，获取到了一个全局的服务对象。  

### 1.2.2 揭秘inflater() 
`inflater()` 函数有多个重载，但最终都会执行到 `inflate(XmlPullParser parser, @Nullable ViewGroup root, boolean attachToRoot)` 中。可以看到，第一个参数稍有特殊，是一个 `XmlPullParser` 对象，可以猜到，我们上面带资源id参数的那个函数中最终会通过资源id创建一个 `XmlPullParser` 对象，然后调用该函数。   

先看看这个过程是如何进行的吧。  
 
### 1.2.3 获取 `XmlPullParser` 对象

`LayoutInflater.java`   

```
public View inflate(@LayoutRes int resource, @Nullable ViewGroup root, boolean attachToRoot) {
        // 获取到Resources对象
        final Resources res = getContext().getResources();
        ...
        // 通过Resources对象，根据资源Id创建XmlResourceParser
        final XmlResourceParser parser = res.getLayout(resource);
        try {
            // 调用到最终的核心函数
            return inflate(parser, root, attachToRoot);
        } finally {
           // 
            parser.close();
        }
    }
```  

上面这段代码中，获取到了一个 `XmlResourceParser` 对象，根据前面的分析可以知道，它的肯定也属于 `XmlPullParser` 类型。后面再详细的分析这个对象，现在先继续看看它的获取过程。  

`Resources.java`  

```
public XmlResourceParser getLayout(@LayoutRes int id) throws NotFoundException {
    return loadXmlResourceParser(id, "layout");
}
```
进一步调用 `loadXmlResourceParser()`。  

`Resources.java` 

```
XmlResourceParser loadXmlResourceParser(@AnyRes int id, @NonNull String type)
        throws NotFoundException {
    // 临时用于储存资源值的对象
    final TypedValue value = obtainTempTypedValue();
    try {
        final ResourcesImpl impl = mResourcesImpl;
        // 获取资源，并保存到TypeValue中
        impl.getValue(id, value, true);
        if (value.type == TypedValue.TYPE_STRING) {
        // 如果获取到的资源实际类型为String类型，
        // 进一步解析资源值，获取XmlResourceParser对象
            return impl.loadXmlResourceParser(value.string.toString(), id,
                    value.assetCookie, type);
        }
    ...
    } finally {
        // 释放TypeValue占用的内存
        releaseTempTypedValue(value);
    }
}
```  

在进一步：

`ResourcesImpl.java`

```
XmlResourceParser loadXmlResourceParser(@NonNull String file, @AnyRes int id, int assetCookie,
            @NonNull String type)
            throws NotFoundException {
        if (id != 0) {
            try {
                synchronized (mCachedXmlBlocks) {
                    ...
                    for (int i = 0; i < num; i++) {
                        if (cachedXmlBlockCookies[i] == assetCookie && cachedXmlBlockFiles[i] != null
                                && cachedXmlBlockFiles[i].equals(file)) {
                            // 先检查相同的asset有没有缓存，
                            // 有就直接取缓存的XmlBlock来创建XmlResourceParser
                            return cachedXmlBlocks[i].newParser();
                        }
                    }

                    // 如果没有缓存，就创建一个XmlResourceParser
                    // 然后缓存起来
                    final XmlBlock block = mAssets.openXmlBlockAsset(assetCookie, file);
                    if (block != null) {
                        ...
                        // 创建Parser，它是XmlResourceParser的一个实现类
                        return block.newParser();
                    }
                }
            }
            ...
    }

```

可以看到，前面根据资源id查询到的资源名称后，再借助 `AssertManager` 就可以获得一个 `XmlBlock` 对象，从而创建 `XmlBlock.Parser` 对象。  

`AssertManager.java`  

```
final XmlBlock openXmlBlockAsset(int cookie, String fileName)
        throws IOException {
        synchronized (this) {
            ...
            // 在native层创建一个对应的资源信息持有对象，然后获得其指针
            long xmlBlock = openXmlAssetNative(cookie, fileName);
            if (xmlBlock != 0) {
                // 创建一个XmlBlock
                XmlBlock res = new XmlBlock(this, xmlBlock);
                incRefsLocked(res.hashCode());
                return res;
            }
        }
        ...
    }
```  

上面的关键点是在 `openXmlAssetNative()` 函数，从这一步就将实际的解析操作转交给了native层进行了。可以跟进一点大概了解下。  

`/frameworks/base/core/jni/android_util_AssetManager.cpp`   

```
static jlong android_content_AssetManager_openXmlAssetNative(JNIEnv* env, jobject clazz,
                                                        jint cookie,
                                                         jstring fileName)
{
	// 获得底层的AssetManager指针
    AssetManager* am = assetManagerForJavaObject(env, clazz);
    ...
    // 先通过AssetManager获取到全局的资源表ResTable
    // 然后再通过cookie查找或创建一个动态资源引用表DynamicRefTable对象
    const DynamicRefTable* dynamicRefTable =
            am->getResources().getDynamicRefTableForCookie(assetCookie);
    // 创建包含了xml资源信息的对象，获得其指针        
    ResXMLTree* block = new ResXMLTree(dynamicRefTable);
    ...
    // 返回资源表的指针给Java层
    return reinterpret_cast<jlong>(block);
}
```
上面这段代码可以看出，在底层也有一个 `AssetManager` 对象，真正获取资源就是通过它获取到的。得到了全局资源表 `ResTable` 后，进一步的会去获取到指定的资源引用，然后再创建包含资源信息的 `ResXMLTree` 对象， `ResXMLTree` 继承自 `ResXMLParser` ，可以通过它来解析xml资源。    

将 `ResXMLTree` 的指针返回给Java层之后，Java层就可以操作它来解析获取xml资源中的值了。  

至此，已经根据资源id获取到了对应的资源的 `XmlPullParser` 对象，在深入的以后再分析。现在回到 `inflater()` 中重点看如何解析映射出View实例。  

### 1.2.4 回到inflater()

`LayoutInflater.java`   

```
public View inflate(XmlPullParser parser, @Nullable ViewGroup root, boolean attachToRoot) {
    ...
    // 将XmlPullParser转化为AttributeSet
    // 如果XmlPullParser属于AttributeSet类型，直接强转，
    // 否则会基于它创建一个AttributeSet对象
    final AttributeSet attrs = Xml.asAttributeSet(parser);
    // inflater的基View
    View result = root;
    ....
```
先将传入的 `XmlPullParser` 转换为 `AttributeSet` ，为后面映射创建View做准备。   

`LayoutInflater.java`   

```
public View inflate(XmlPullParser parser, @Nullable ViewGroup root, boolean attachToRoot) {
    ...
    try {
                int type;
                while ((type = parser.next()) != XmlPullParser.START_TAG &&
                        type != XmlPullParser.END_DOCUMENT) {
                    // Empty
                }
                if (type != XmlPullParser.START_TAG) {
                    throw new InflateException(parser.getPositionDescription()
                            + ": No start tag found!");
                }
                
                // 以上先确保一开始解析到的是布局的开始
                final String name = parser.getName();
                ...
                if (TAG_MERGE.equals(name)) {
                    ...
                    // 如果是merge标签，直接走rInflater()
                    // 这个函数后面再看
                    rInflate(parser, root, inflaterContext, attrs, false);
                } else {
                    // 正常的标签，通过createViewFromTag()来创建View
                    // 这个函数后面再看
                    final View temp = createViewFromTag(root, name, inflaterContext, attrs);
                    
                    // 接下来是要设置View的LayoutParams
                    ViewGroup.LayoutParams params = null;
                    if (root != null) {
                        ...
                        params = root.generateLayoutParams(attrs);
                        if (!attachToRoot) {
                         temp.setLayoutParams(params);
                        }
                    }
                    ...
                    // 实际调用了rInflate()
                    rInflateChildren(parser, temp, attrs, true);
                    // 将View添加到容器中
                    if (root != null && attachToRoot) {
                        root.addView(temp, params);
                    }

                    if (root == null || !attachToRoot) {
                        result = temp;
                    }
                }

....
```  

上面这段代码中，主要会找到xml的开始标签，然后获取到标签名称，如果是 `merge` 类型，直接走 `rInflater()` 创建View，这样就能减少布局层级。否则，通过 `createViewFromTag()` 创建根View，然后再递归调用`rInflater()` 创建View。实际上，在 `rInflater()` 中也会调用 `createViewFromTag()` 创建节点View。  

所以先分析 `createViewFromTag()` 是如何创建出View来的。  

###  1.2.5 `createViewFromTag()` 创建View

`LayoutInflater.java`   

```
View createViewFromTag(View parent, String name, Context context, AttributeSet attrs,
            boolean ignoreThemeAttr) {
   if (name.equals("view")) {
        name = attrs.getAttributeValue(null, "class");
    }
    ...
    try {
            View view;
            /**
             * 通过Factory来创建View。LayoutInflater包含了多个Factory
             * 它们的优先级为：
             * mFactory2 > mFactory > mPrivateFactory
             * mFactory2和mPrivateFactory是Factory2类型，能够获取到父View
             * mFactory是Factory类型，不能获取到父View。
             * 这几个Factory均可以通过外部自定义注入，就是说我们可以自定义View的创建逻辑。
             */
            if (mFactory2 != null) {
                view = mFactory2.onCreateView(parent, name, context, attrs);
            } else if (mFactory != null) {
                view = mFactory.onCreateView(name, context, attrs);
            } else {
                view = null;
            }

            if (view == null && mPrivateFactory != null) {
                view = mPrivateFactory.onCreateView(parent, name, context, attrs);
            }
            // 如果前面View没有创建成功，那么会通过成员函数onCreateView()来创建View
            if (view == null) {
                final Object lastContext = mConstructorArgs[0];
                mConstructorArgs[0] = context;
                try {
                    if (-1 == name.indexOf('.')) {
                    // 不带“.”说明是系统的View
                        view = onCreateView(parent, name, attrs);
                    } else {
                        view = createView(name, null, attrs);
                    }
                } 
                ...
            }

            return view;
        }                 
   ...         
```
从代码可以看到，View的创建主要有两种方式，一种是通过Factory来创建，一种是通过 `LayoutInflater` 的成员函数 `createView()` 来创建。  

`createView()` 中创建View主要是通过View的类名 `name` 反射到对应的构造函数，然后传入 `AttributeSet` 等参数创建对应的View实例。思路比较简单，具体代码就不再分析了。   

View类名就是通过 `XmlPullParser` 解析xml获得的标签名。 

当然，也可以自定义一个Factory，然后传入LayoutInflater中，用于创建View。  


### 1.2.6 rInflate()解析View树

```
void rInflate(XmlPullParser parser, View parent, Context context,
            AttributeSet attrs, boolean finishInflate) throws XmlPullParserException, IOException {
    ...
    // 创建View        
    final View view = createViewFromTag(parent, name, context, attrs);
    final ViewGroup viewGroup = (ViewGroup) parent;
    final ViewGroup.LayoutParams params =   viewGroup.generateLayoutParams(attrs);
    // 递归创建子View
    rInflateChildren(parser, view, attrs, true);
    // 将创建的View添加到它的父View中
    viewGroup.addView(view, params);
    ...
```

到此，就把一个布局文件映射成我们需要的View了。


# 2. Android中的XmlPullParser介绍

在上面的分析中可以知道，xml的解析依赖于XmlPullParser解析器。这是一种基于流的解析器，用于解析xml文件。  

`XmlPullParser` 实际是一个接口，定义了xml的解析操作。

## 2.1 XmlPullParser常用操作
在上面的代码分析中，我们已经看到了一些XmlPullParser的操作，现在来看看这些操作都是在干什么。  

### 2.1.1 next()函数
`next()` 能够访问到一个事件，并且可以返回该事件的事件类型。  

注： `getEventType()` 能够获取当前事件的类型。

### 2.1.2 XmlPullParser的事件类型
1. `XmlPullParser.START_DOCUMENT`：表示解析文档开始。  


2. `XmlPullParser.END_DOCUMENT`：表示解析文档结束。

3. `XmlPullParser.START_TAG`：表示当前事件为一个标签的开始。

4. `XmlPullParser.END_TAG`：表示当前事件为一个标签的结束。

5. `XmlPullParser.TEXT`：表示当前事件为一个标签中的值。  

### 2.1.3 getName()函数
这个函数可以获取当前标事件标签的名称。如：  

```
<TextView
        android:id="@+id/tv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        ...
    />
```  
上面这段xml的 `mXmlPullParser.getName()` 获得的值为 `TextView` 。


### 2.14 获取属性
```
<TextView
        android:id="@+id/tv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        ...
    />
```  

获取上面xml的属性：  

```
// 获取该标签的属性个数 - 3
mXmlPullParser.getAttributeCount();

// 获取该标签的第一个属性的名称 - android:id
mXmlPullParser.getAttributeName(0);

// 获取该标签第一个属性的值 - @+id/tv
mXmlPullParser.getAttributeValue(0);
```  

## 2.2 XmlResourceParser解密

在 **1.2.3** 中，我们获取到的 `XmlPullParser`     实际是 `XmlResourcesParser`，这也是一个接口，同时还继承了 `AttributeSet`，而在 `LayoutInflater.inflate()` 中的实际实现是 `XmlBlock.Parser` 。  

![XmlResourcesParser类](https://gw.alicdn.com/tfs/TB1FLQAjwvD8KJjy0FlXXagBFXa-470-390.png)

因此，在 `LayoutInflater.inflate()` 中可以通过 `XmlResourcesParser` 解析xml，同时可以把它直接转换为 `AttributeSet`。   



## 2.3 AttributeSet
`AttributeSet` 是一个接口，定义了一组用于解析和获取xml中节点属性的操作。

在View的构造函数中，我们经常看到它的身影。实际上，从上面的分析可以知道，在创建View的时候，需要反射调用包含该参数的2参构造函数。

```
public RelativeLayout(Context context, AttributeSet attrs) {
    ...
}
```

本次关于 `LayoutInflater` 的分析就到着，现在已经知其所以然了。

