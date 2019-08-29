- 代理类，帮助创建代理类

# getProxyClass(ClassLoader loader, Class<?>... interfaces)
- 可通过一系列接口动态创建代理类，该代理类实现了这些接口；
- 传null会使用系统默认ClassLoader；  

```
if (loader == null) {
            loader = ClassLoader.getSystemClassLoader();
        }
```
- 任何一个interface为空报错；

```
if (interfaces == null) {
            throw new NullPointerException("interfaces == null");
        }
```
- interfaces必须是接口，否则报错；

```
if (!c.isInterface()) {
                throw new IllegalArgumentException(c + " is not an interface");
            }
```
- interfaces必须由是由ClassLoad加载的，或者能够通过ClassLoader成功加载，否则报错；
```
if (!isVisibleToClassLoader(loader, c)) {
                throw new IllegalArgumentException(c + " is not visible from class loader");
            }

            
private static boolean isVisibleToClassLoader(ClassLoader loader, Class<?> c) {
        try {
            return loader == c.getClassLoader() || c == Class.forName(c.getName(), false, loader);
        } catch (ClassNotFoundException ex) {
            return false;
        }
    }

```
- 会把缓存的类放到ClassLoader的proxyCache中

```
loader.proxyCache.put(interfaceList, result);

 //缓存ClassLoader中的类
 public final Map<List<Class<?>>, Class<?>> proxyCache =
            new HashMap<List<Class<?>>, Class<?>>();
```
- 返回的Class的创建是调用了一个Native方法

```
private static native Class<?> generateProxy(String name, Class<?>[] interfaces,
                                                 ClassLoader loader, Method[] methods,
                                                 Class<?>[][] exceptions);
```

# newProxyInstance(ClassLoader loader, Class<?>[] interfaces,InvocationHandler invocationHandler)
- 根据参数创建并返回代理类的实例对象

```
return getProxyClass(loader, interfaces)
                   //根据Class类型，动态创建代理类的构造器，并返回这个构造器，底层是Native方法 .getConstructor(InvocationHandler.class)
                   //使用上面创建的构造器创建实例 .newInstance(invocationHandler);
```

# getInvocationHandler(Object proxy)
- 返回指定对象的的调用处理器，proxy必须是一个代理类，否则报错；

```
if (!(proxy instanceof Proxy)) {
            throw new IllegalArgumentException("not a proxy instance");
        }
        return ((Proxy) proxy).h;
```

# InvocationHandler
调用处理器，负责调用方法。代理类中的方法实现就是在这个类的invoke()中实现的。  
换句话说，我们所调用的代理的方法，其实调用的是InvocationHandler的invoke()方法。  

Retrofit中的InvocationHandler：  

```
new InvocationHandler() {
          private final Platform platform = Platform.get(); //根据指定包名判断，并创建一种类型的Platform，Android、Java、IOS

          @Override public Object invoke(Object proxy, Method method, Object... args)
              throws Throwable {
            // If the method is a method from Object then defer to normal invocation.
            if (method.getDeclaringClass() == Object.class) {
              return method.invoke(this, args);
            }
            if (platform.isDefaultMethod(method)) { //这个方法目前看到的均返回false，包括Platform的实际类型为Android、Java、IOS
              return platform.invokeDefaultMethod(method, service, proxy, args);
            }
            ServiceMethod serviceMethod = loadServiceMethod(method); //定义方法的帮助类
            OkHttpCall okHttpCall = new OkHttpCall<>(serviceMethod, args);
            return serviceMethod.callAdapter.adapt(okHttpCall); //返回OkHttpCall
          }
        });
```


