![image](http://upload-images.jianshu.io/upload_images/1869462-c0a53dd3b1d4db5d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  


# AIDL的使用

## 第一步 创建aidl接口文件

![image](http://upload-images.jianshu.io/upload_images/1869462-688a38a60607dc01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

AndroidStudio中直接右键创建，或者自己一步步建目录喽。  

创建完成后会生成一个`XXX.aidl`接口文件，我们需要根据需求在这个接口类中添加接口。  

在看接口怎么写前，先记住以下三点：

### 支持的参数类型
1. 八种基本数据类型；
2. String、CharSequence；
3. List、Map，它们中的数据类型也应该是AIDL支持的；
4. 实现Parcelabel的引用类型。 

### 自定义引用类型使用
如果要使用自定义的数据类型，需要先为它也生成一个aidl文件，里面内容只需两行：

```
AIDL的包名;

parcelable 类名;
```
例如：

```
package com.coorchice.coorchicelibone;

parcelable Role;
```
接着就可以创建对应的java数据类了，然后实现Parcelable接口，注意包名需要和aidl一模一样！如果你对自定义类型使用了in或者inout标识符的话，你必须再给自定义类实现`readFromParcel()`方法。比如：

```
public void readFromParcel(Parcel in) {
  name = in.readString();
  skill = in.readString();
}
```

然后在定义aidl接口时，一定要记得手动写一下自定义引用类的import！


### 参数修饰符
- **in**: 表示参数数据只能由客户端传递到服务端，基本类型就默认只支持in修饰符。
- **out**：表示参数数据只能由服务端传递到客户端。即服务端如果修改了参数对象的值，那么客户端的值也会变化，但是服务端无法读取到客户端对象的值。
- **inout**：表示参数数据能够双向传递。   


### 定义服务接口
现在，我们可以开始定义服务接口了！这里定义的服务接口就是后面我们需要在客户端调用的。

```
package com.coorchice.coorchicelibone;

// 此处必须注意，一定要手动导入自定义的类
import com.coorchice.coorchicelibone.Role;

interface AIDLDemo {

  void studySkill(out Role role);

  void changeName(String newName, inout Role role);

  void createRole(in Role name);
}
```
CoorChice定义了3个接口，接着编译一下。然后编译器会自动根据我们定义的接口生成对应的类。  

> 注意：自定义的类必须加in、out、inout等标识符，否则会报错！  

### 编译后生成的类解析
编译之后编译器自动帮我们生成了一个`服务接口类名.Stub`的抽象类，它继承自Binder，然后实现了我们定义的服务接口（注意我们的服务接口实现了`IInterface`接口）。嗯，重点是它是一个Binder！它就是我们用来进行Binder通讯的！  


其实，Android的AIDL就是让编译器帮助我们生成一个实现了我们接口的Binder，以帮助我们简化开发。当然，如果你了解原理的话，也可以自己写。  

下面我们一步一步来看看这编译器生成的类都有些什么？  

#### 1. 服务接口实现IInterface

```
public interface AIDLDemo extends android.os.IInterface
{
    ...
}
```
编译器根据我们写的服务接口，重新生成了一个接口，唯一的区别就是新的接口继承了`IInterface`接口。这个接口是干什么的呢？

```
public interface IInterface
{
    public IBinder asBinder();
}
```
可以看到，它只有一个接口方法。这个方法用来定义将实现接口的类应该具备返回一个与之相关联的Binder的功能，以提供通讯能力。一般实现这个方法的类自己本身就会去继承Binder。  

这样的设计使得Binder机制不用关心具体的接口是什么，只要是`IInterface`就行。事实上相当于是我们在`IInterface`接口的基础上扩展了接口功能，本质上还是一个`IInterface`，所以Binder能够认出它。  

#### 2. 继承Binder，实现服务接口
要进行Binder通讯，我们自然需要一个Binder；要实现我们定义的服务接口功能，自然就需要实现服务接口。那么需要满足这两个条件怎么办？很简单，继承Binder，然后实现服务接口就行。  

编译器为我们生成的类中有一个内部类`Stub`就是这么干的。事实上，这样的设计随处可见。你可以看看CoorChice这篇文章[《3分钟看懂Activity启动流程》http://www.jianshu.com/p/9ecea420eb52](http://www.jianshu.com/p/9ecea420eb52)中出现的`ActivityManagerNative`就是这么干的，虽然它是代理系统的ActivityManagerService，但是模式都是一样的。  

```
public static abstract class Stub extends android.os.Binder implements com.coorchice.coorchicelibone.AIDLDemo
```

#### 3. Binder需要绑定服务接口，定义DESCRIPTOR描述
为了一个Binder和一个特定服务接口绑定，以对外提供功能，需要给Binder定义一个`DESCRIPTOR`描述，表示我这个Binder是提供特定功能链接的，不是随便可以用的。  

通常，`DESCRIPTOR`描述会直接使用`包名 + 服务接口`。  

```
private static final java.lang.String DESCRIPTOR = "com.coorchice.coorchicelibone.AIDLDemo";  
public Stub()
{
// 设置Binder的描述服务接口和标识描述
this.attachInterface(this, DESCRIPTOR);
}
```

#### 4. 实现asInterface()供客户端调用
作为一个服务提供者，为了能够给调用者提供远程功能，自然需要能够提供和远程服务关联的Binder来通讯，请求服务。获取Binder的接口就是IInterface中定义的。

```
public static com.coorchice.coorchicelibone.AIDLDemo asInterface(android.os.IBinder obj)
{
    if ((obj==null)) {
        return null;
    }
        android.os.IInterface iin = obj.queryLocalInterface(DESCRIPTOR);
        if (((iin!=null)&&(iin instanceof com.coorchice.coorchicelibone.AIDLDemo))) {
        return ((com.coorchice.coorchicelibone.AIDLDemo)iin);
    }
    // 创建代理
     return new com.coorchice.coorchicelibone.AIDLDemo.Stub.Proxy(obj);
}
```
先查询一下获取到的和远程Service通讯的Binde中是否已经添加了功能接口的实现，如果没有则创建代理，通过代理间接的操作Binder和远程Service通讯，实现功能。  

##### 思考：既然我们已经获取到了能够直接和远程Service通讯的Binder，为什么不直接操作它去向远程Service请求服务呢？
确实，我们可以直接操作Binder向远程Service请求服务，但这过程中有很多繁琐的操作，还有一些code码的区别，如果不封装隔离的话，随着功能的扩展，我们将很难再去维护这段通讯逻辑。还有就是通过这种方式统一的管理通讯逻辑使得它可以随处使用，而不用没一个要用的地方都去写一遍。

既然是要代理和远程服务通讯，而且通讯的目的是为了请求服务接口定义的功能，那么很自然就能想到去实现服务接口，然后再对应的接口方法中实现逻辑即可。这样就可以分开来维护客户端和服务端的对应的每个功能了。  

所以，我们的代理也需要实现服务接口，然后在代理中操作远程通讯的Binder进行通讯。  

#### 5. 重写onTransact()
在Binder通讯中，一个和远程端通讯的`Binder::transact()`方法，会触发通讯目标端执行`Binder::onTransact()`，就是说这个时候已经收到了通讯发起端的请求了。看看这个方法的参数：

```
public boolean onTransact(int code, Parcel data, Parcel reply, int flags){}
```

参数 | 解释
---|---
code | 用来标识指令，即这次通讯是使用什么功能。需要客户端和服务端约定好code码。
data | 来自发送端的数据包。
reply | 来自发送端的接收包，往这个包中写数据，就相当于给发送端返回数据。
flags | 特殊操作标识。  

对应的我们在看看`Binder::transact()`方法：

```
public boolean transact(int code, Parcel data, Parcel reply, int flags){}
```
参数 | 解释
---|---
code | 用来标识指令，即这次通讯是使用什么功能。需要客户端和服务端约定好code码。
data | 发送给远程端的数据包。
reply | 用于让远程端写回复数据的数据包
flags | 特殊操作标识。 

可以看出来，两个是成对的操作。`Binder::transact()`操作是一个阻塞式的操作，就是说在这个方法执行返回成功后，直接从reply中读取的数据就是远程端在`Binder::onTransact()`中填充的数据。  

这个过程可以大概抽象成这个样子：  

![image](http://upload-images.jianshu.io/upload_images/1869462-abc960d4bf758396.gif?imageMogr2/auto-orient/strip)  

在编译器自动帮我们生成的`onTransact()`中，会读取data中数据，然后调用对应的方法（这个方法还没实现，所以我们可以继承Stub然后重写，以实现在服务端处理的效果）。比如这个样子：

```
case TRANSACTION_studySkill:
{
data.enforceInterface(DESCRIPTOR);
com.coorchice.coorchicelibone.Role _arg0;
_arg0 = new com.coorchice.coorchicelibone.Role();
this.studySkill(_arg0);
reply.writeNoException();
if ((_arg0!=null)) {
// 调用重写的方法
reply.writeInt(1);
_arg0.writeToParcel(reply, android.os.Parcelable.PARCELABLE_WRITE_RETURN_VALUE);
}
else {
reply.writeInt(0);
}
return true;
}
```

## 第二步. 创建一个远程Service
我们正常创建一个支持其它应用调用的Service，Service怎么创建就不说。主要看看在Service最重要的一步，就是继承上面生成的Stub，然后自定义一个Binder。

```
final class AIDLDemoBinder extends AIDLDemo.Stub{

     @Override
     public boolean onTransact(int code, Parcel data, Parcel reply, int flags) throws RemoteException {
       // 在这里可以优先获取数据校验，返回false可以限制客户端和Service继续通讯
       return super.onTransact(code, data, reply, flags);
     }

     @Override
     public void studySkill(Role role) throws RemoteException {
        //当客户端调用Binder的同名方法时，会调用到这里的代码
     }

     @Override
     public void changeName(String newName, Role role) throws RemoteException {
        //当客户端调用Binder的同名方法时，会调用到这里的代码
     }

     @Override
     public void createRole(Role name) throws RemoteException {
        //当客户端调用Binder的同名方法时，会调用到这里的代码
     }
   }
```

接着，在`onBinder()`中返回一个上面这个Binder的实例给客户端。  

## 第三步. 客户端链接Binder
首先重要的一步是，我们必须把这个aidl文件夹拷贝到客户端工程的对应目录下。  

**包名不能变！**  

**包名不能变！**  

**包名不能变！** 

然后通过绑定的方式启动这个Service。  


```
if(AppUtils.PackageIsExist(Service的包名)){
    Intent intent = new Intent();
    intent.setPackage(getPackageName());
    intent.setComponent(new ComponentName(Service的包名, Service包括包名的完整名称));
    
    // 
    ServiceConnection mConnection = new ServiceConnection() {
    @Override
    public void onServiceConnected(ComponentName componentName, IBinder iBinder) {
      AIDLDemo mRemote = AIDLDemo.Stub.asInterface(iBinder);
      try {
        // 效果就像直接调用Service中的方法一样！！
        mRemote.studySkill(new Role());
      } catch (RemoteException e) {
        e.printStackTrace();
      }
    }
    @Override
    public void onServiceDisconnected(ComponentName componentName) {
      
    }
 };
    
    bindService(intent, mConnection, Context.BIND_AUTO_CREATE);
}
```

# 总结

恭喜你！现在你已经掌握AIDL了！  

实际上，从上面的分析可以看出，AIDL其实就是对Binder机制的简化封装。Android这一套封装使得我们在自己定义Service时方便了许多！你可以不用去编写繁杂的交互，看看编译器自动生成的文件有多不堪入目吧。  

但是不管怎么封装，Binder通讯机制的灵魂是不变的，所以要更好的理解这个过程，你可以看看CoorChice的这几篇文章：  

[《从getSystemService()开始，开撸Binder通讯机制》http://www.jianshu.com/p/1050ce12bc1e](http://www.jianshu.com/p/1050ce12bc1e)；  

[《能用【白话文】来分析Binder通讯机制？》http://www.jianshu.com/p/fe816777f2cf](http://www.jianshu.com/p/fe816777f2cf)；  

[《Binder机制之一次响应的故事》http://www.jianshu.com/p/4fba927dce05](http://www.jianshu.com/p/4fba927dce05)  

这几篇文章从我们接触最多的上层入手，一步步分析到了Binder内核层，描述了内核的Bidner驱动是如何实现一次完整的c/s通讯的。  

> - 抽出空余时间写文章分享需要动力，还请各位看官动动小手点个赞，给CoorChice鼓励吧😄
> - CoorChice一直在不定期的分享新的干货，想要上车只需进到CoorChice的【个人主页】点个关注就好了哦。发车喽～



