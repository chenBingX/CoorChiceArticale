# Android 注解


> 注解是一种元数据, 可以添加到java代码中. 类、方法、变量、参数、包都可以被注解，注解对注解的代码没有直接影响.

```
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}
```

这是常用的 `@override` 注解的源码。  

可以看出，定义注解使用关键字 `@infterface`。

# 1. 先了解 元注解

元注解就是用来定义注解的注解。就像是一些最基本的元素一样。  

## 1.1 @Retention
定义注解的可用范围。默认值为 `RetentionPolicy.CLASS` 。


| SOURCE | 只在源码中可用 |
| --- | --- |
| CLASS | 在源码、字节码中可用 |
| RUNTIME | 在源码、字节码、运行时可用 |

eg:  


```
@Retention(RetentionPolicy.RUNTIME)
public @interface Test{
}
```

## 1.2 @Target
定义注解可用于注解哪些元素。没有写表示可以用于所有的元素。  

```
public enum ElementType {
    /** 可修 class、interface、enum */
    TYPE,

    /** 修饰属性 */
    FIELD,

    /** 修饰方法 */
    METHOD,

    /** 修饰参数 */
    PARAMETER,

    /** 修饰构造器 */
    CONSTRUCTOR,

    /** 修饰本地变量 */
    LOCAL_VARIABLE,

    /** 表示注解可修饰注解 */
    ANNOTATION_TYPE,

    /** 修饰包 */
    PACKAGE,

    /**
     * 表示该注解能写在类型变量的声明语句中
     *
     * @since 1.8
     */
    TYPE_PARAMETER,

    /**
     * 表示该注解能写在使用类型的任何语句中
     *
     * @since 1.8
     */
    TYPE_USE
}

```

eg:  

```
@Target(ElementType.TYPE, ElementType.TYPE_PARAMETER)
public @interface Test{
}
```

## 1.3 @Inherited

定义注解是否可被继承，默认不写为 false。    

eg:

```
@Inherited
public @interface Test{
}
```

如，该注解如果注解了一个父类中的抽象方法，那么子类将会继承该注解。  

一般的注解是不会被继承的。 


## 1.4 @Documented

定义注解是否会被保存到文档中。  

```
@Documented
public @interface Test{
} 
```  

在生成 Javadoc 时，该注解也会生成到文档中。


# 2. 开始自己的 ButterKnife 吧！

## 2.1 如何自定义注解

使用 `@interface` 来定义一个注解，然后使用元注解来修饰注解。    

注解中的属性定义格式：  

```
 /**
  * value 可以直接写值在括号中
  * 如果是别的名称，使用时可能是这样的：@BindView(id = R.id.tv)
  */
类型 名称() default 默认值;
```


eg:

```
/**
 * 只能作用于属性上
 */
@Target(ElementType.FIELD)
/**
 * 可以作用于运行时
 */
@Retention(RetentionPolicy.RUNTIME)
public @interface BindView {
    /**
     * value 可以直接写值在括号中
     * 如果是别的名称，使用时可能是这样的：@BindView(id = R.id.tv)
     */
    int value() default -1;
}
```

## 2.2 运行时如何使用自定义的注解

当一个注解被使用时，我们可以通过反射来获取它，并且获取到注解中的值。所以，定义好注解后，还需要写一个处理器来处理注解逻辑。  

比如，我们定一个 `ButterKnife` 处理器。  

```
public class ButterKnife {
    public static void bind(Activity act){
        if (act != null){
            Class<? extends Activity> cls = act.getClass();
            // 获取Activity的所有成员变量
            Field[] fields = cls.getDeclaredFields();
            for (Field f:fields){
                // 尝试取出 BindView 类型的注解，如果没有就会为 null
                BindView annotation = f.getAnnotation(BindView.class);
                if (annotation != null){
                    // 取出注解的值
                    int id = annotation.value();
                    f.setAccessible(true);
                    try {
                        // 获得属性的类型
                        Class viewClass = f.getType();
                        // 根据id获取view对象，然后强制转换，设置属性的值
                        f.set(act, viewClass.cast(act.findViewById(id)));
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }

        }
    }
}
```

现在可以使用了！  

```
public class AnnotationActivity extends AppCompatActivity {

    /**
     * 注解
     */
    @BindView(R.id.tv)
    TextView tv;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_annotation);
        /**
         * 一定要在 setContentView() 后调用
         */
        ButterKnife.bind(this);

        /**
         * 此时，tv已经被赋值了
         */
        tv.setOnClickListener(v -> tv.setTextColor(Color.RED));
    }
}
```

效果显示，生效了!

![](https://raw.githubusercontent.com/chenBingX/img/master/其它/annotion例子.gif)

