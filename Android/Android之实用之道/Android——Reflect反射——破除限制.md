> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

# 简单介绍
java的反射机制使java成为一种动态语言，通过反射机制能够在运行时或的一个对象的所有信息，包括他的包名，它的所有方法和成员变量。当然知道包名可以直接取得该类，从而获得他的实例。
# 获取Class对象
```
//通过对象获得：
class = b.getClass();

//通过完整包类名获得：
class = Class.fromName("包名.类名")

//本包可以通过.class获得：
class = 类名.class
```
# 通过反射调用私有方法
```
//获得类
Class<?> c1 = persen.getClass();
try {
    //通过方法名获得方法，这是私有方法
    Method method1 = c1.getDeclaredMethod("method1");
    //调用方法私有方法的关键
    method1.setAccessible(true); //设置该私有方法可被调用
    //方法调用
    method1.invoke(c1.newInstance());

    //这是带参数的方法，需要在获得方法的时候把参数的类型都加上
    Method method2 = c1.getDeclaredMethod("method2",String.class);
    //调用方法，填写参数
    method2.invoke(c1.newInstance(),"kk");

    //这是有返回值的方法
    Method method3 = c1.getDeclaredMethod("method3");
    return (int) method3.invoke(c1.newInstance());
} catch (NoSuchMethodException | InvocationTargetException | IllegalAccessException | InstantiationException e) {
    e.printStackTrace();
}
```
# 通过反射操作私有属性
```
Class c1 = persen.getClass();
try {
    //通过属性名获得属性
    Field name = c1.getDeclaredField("name");
    //由于是私有属性，所以需要设置它可见
    name.setAccessible(true);
    //直接修改该对象的该属性的值
    name.set(persen,"小明");
    //获得该对象的该属性的值
    return (String) name.get(persen);
} catch (NoSuchFieldException e) {
    e.printStackTrace();
} catch (IllegalAccessException e) {
    e.printStackTrace();
}
```
# 奉上一个调用反射的帮助类
```
public static Method getMethod(@NonNull Class<?> clazz, @NonNull String methodName) throws NoSuchMethodException {
    Method method = clazz.getDeclaredMethod(methodName);
    method.setAccessible(true);
    return method;
  }

  public static Method getMethod(@NonNull Class<?> clazz, @NonNull String methodName,
      Class... parameterTypes) throws NoSuchMethodException {
    Method method = clazz.getDeclaredMethod(methodName, parameterTypes);
    method.setAccessible(true);
    return method;
  }

  public static Method getApiMethod(@NonNull Class<?> clazz, @NonNull String methodName)
      throws NoSuchMethodException {
    Method[] methods = clazz.getDeclaredMethods();
    for (Method method : methods) {
      if (method.getName().contains(methodName)) {
        method.setAccessible(true);
        return method;
      }
    }
    return null;
  }



  public static void invokeMethod(@NonNull Class<?> clazz, @NonNull String methodName) throws IllegalAccessException, InstantiationException, InvocationTargetException, NoSuchMethodException {
    getMethod(clazz, methodName).invoke(clazz.newInstance());
  }

  public static <T> void invokeMethod(T t, @NonNull String methodName)
      throws IllegalAccessException, InstantiationException, InvocationTargetException,
      NoSuchMethodException {
    getMethod(t.getClass(), methodName).invoke(t);
  }

  public static <T, V> void invokeApiMethod(T t, @NonNull String methodName, V v,
      Class... parameterTypes) throws IllegalAccessException, InstantiationException,
      InvocationTargetException, NoSuchMethodException {
    getApiMethod(t.getClass(), methodName).invoke(t, v);
  }

  public static Field getVariable(@NonNull Class<?> clazz, @NonNull String variableName) throws NoSuchFieldException {
    Field variable = clazz.getDeclaredField(variableName);
    variable.setAccessible(true);
    return variable;
  }
```

![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-479e9467efa1e8d9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
