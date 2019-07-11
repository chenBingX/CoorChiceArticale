# 重要的事情说3遍
请使用 **Andorid Studio 2.2** 及以上版本！  

请使用 **Andorid Studio 2.2** 及以上版本！  

请使用 **Andorid Studio 2.2** 及以上版本！  

# 下载安装NDK开发环境
[对着这个说明一步一步搞，1分钟妥妥的集成](https://developer.android.com/studio/projects/add-native-code.html#link-gradle)    

看完这个链接再接着往下看啊！尤其是 **CMake** 的配置部分，需要认真看下。

# 将原生代码编译成.os
照着上一步一切顺利的话，就可以尝试开始这一步了。  

首先在模块的`build.gradle`的`android.defaultConfig.externalNativeBuild.cmake{}`和`android.defaultConfig.ndk{}`中添加这一句：

```
abiFilters 'x86', 'x86_64', 'armeabi', 'armeabi-v7a', 'arm64-v8a'
```
看起来是这个样子的：

```
externalNativeBuild {
    cmake {
        cppFlags ""
        abiFilters 'x86', 'x86_64', 'armeabi', 'armeabi-v7a', 'arm64-v8a'
    }
}
ndk {
    abiFilters 'x86', 'x86_64', 'armeabi', 'armeabi-v7a', 'arm64-v8a'
}
```
没有就自己写上。这是用来配置将编译哪几种类型的ABI对应的.os文件。
配置完成后，直接执行`Build > Build APK(s)`。  

![image](http://upload-images.jianshu.io/upload_images/1869462-19b19b71e7efaa7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

编译完成后执行`Build > Analyze APK...` 找到对应的apk，就能看到如下界面，就能找到.os文件。  

![image](http://upload-images.jianshu.io/upload_images/1869462-5241738b950f4bdd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



# 编写/注册c/c++函数

## 静态注册
### 函数命名规则
> Java + 包名 + 类名 + 函数名  

如：
```
JNIEXPORT void JNICALL
Java_com_example_cappdemo_helloCpp(JNIEnv* env, jobject)
{
    
}
```
上面的**JNIEXPORT**和**JNICALL**是JNI的宏，用来标识该函数可以被JNI调用。  

- JNIEnv结构体指向了JNI的函数表，这些函数可以完成和Java的交互。
- jobject是当前与之链接的native方法隶属的类对象，即调用这个JNI方法的对象。  

上面这两个参数由Java虚拟机调用的时候传入。

## 动态注册
1. 由于是将函数映射表注册到JVM中，所以函数的调用速度更快。
2. 不用使用静态注册那套繁琐的命名规则。

### 注册

```
//编写需要使用的函数
static jstring nativeJNITest(JNIEnv *env, jobject) 
{
    std::string test = "你好，c++";
    return env->NewStringUTF(test.c_str());
}

static jint nativeComputeDamage(JNIEnv *env, jobject thiz, jint attack, jint agility)
{
    return (jint)(attack + agility * 1.2)
}

// 提供一个函数映射表，注册给JVM，这样JVM就可以通过函数映射表来调用相应的函数
// 这样的效率比静态注册的效率高
/**
 * @param1 Java中的native方法名。可以自定义。
 * @param2 函数签名，描述函数的返回值和参数
 * @param3 函数指针，指向被调用的c++函数。名车需要和函数名一样。
 */
static JNINativeMethod nativeMethod[] = {
    {"JNITest", "()Ljava/lang/String;", (void *) nativeJNITest},
    {"computeDamage", "(II)I", (void *)nativeComputeDamage}
};

//该函数在执行System.loadLibrary()后会被调用，用于向JVM注册函数表。
//返回值是使用的JNI版本。
JNIEXPORT jint JNICALL JNI_OnLoad(JavaVM *jvm, void *reserved)
{
    JNIEnv *env;
    //需要通过JVM动态的获取JNIEnv来提供Java介质
    if (jvm->GetEnv((void **) &env, JNI_VERSION_1_6) != JNI_OK) {
        return JNI_ERR;
    }
    
    //需要调用这些函数的类
    //一定要注意名称的正确性：包名 + 类名
    jclass clz = env->FindClass("com/example/xingxinyu/cppdemo/JNIHelper");
    if(clz == NULL){
        LOGE("类名不对");
    } else {
        LOGE("类加载成功");
    }
    
    jint method_size = sizeof(nativeMethod) / sizeof(nativeMethod[0]);
   /**
     * 注册函数表
     * @param1 需要关联到那个【Java】类，Kotlin类不行
     * @param2 方法数组
     * @param3 方法数
     */
    env->RegisterNatives(clz, nativeMethod, method_size);
    //返回使用的JNI版本                               
    return JNI_VERSION_1_6;
}
```
### 解注册
向JVM中注册函数映射表后，因该在JVM释放改JNI组件时把其释放，不然就是隐患。

```
JNIEXPORT void JNICALL JNI_OnUnload(JavaVM *jvm, void *reserved)
{
    JNIEnv *env;
    //需要通过JVM动态的获取JNIEnv来提供Java介质
    if (jvm->GetEnv((void **) &env, JNI_VERSION_1_6) != JNI_OK) {
        return;
    }
    jclass clz = env->FindClass("com/example/xingxinyu/cppdemo/JNIHelper");
    // 解注册函数表
    env->UnregisterNatives(clz);
}
```
### JNI描述符
前面在动态注册时，需要生成函数映射表，其中需要一个是`函数签名`，它是由JNI描述符来描述的，写错了函数就会找不到。

#### 基本类型对应关系

Java | JNI
---|---
byte | B
char | C
short | S
int | I
long | J
float | F
double | D
boolean | Z

#### 引用类型描述符
引用类型的描述符各式为：`L + 类相对路径 + ;`。如：

```
// String
Ljava/lang/String;

// Object
Ljava/lang/Object;
```

如果native方法所在的类是一个内部类，则格式为`L + 外部类相对路径 + $ + 内部类名 + ;`。如：

```
// FileStatus
Landroid/os/FileUtils$FileStatus;
```


#### 数组描述符
数组描述符的格式为：`对应维度个[ + 类型描述符`。如：

```
// int[]
[I

// Object[][]
[[Ljava/lang/Object;

```

#### 方法描述符
就是一开始说的函数签名，它的格式为：`(参数描述符) + 返回值描述符`。  

需要注意一点，`void`的描述符是**V**。


```
// String fun()
()Ljava/lang/String;

// void fun(int a, int b)
(II)V

// File fun(byte[] bytes, int length)
([BI)Ljava/io/File;
```

# JNI的数据类型
在普通数据类型前+j，比如：`jobject`对应Java的`obejct`。

# 使用自定义对象

声明以下Java对象
```
package com.coorchice.cppdemo.entry;

public class Hero {
  String race;
  String name;
  int attack;
  int agility;

  @Override
  public String toString() {
    return String.format("名字: %s\n种族: %s\n攻击力: %d\n敏捷: %d", name, race, attack, agility);
  }
}
```
在c/c++中使用该对象的示例如下：

## 使用传入的Java对象
```
// 声明一个结构体，用来保存Hero对象的信息
struct Hero {
    jclass clazz;           // Hero类
    jfieldID hero_name;     // name属性的id
    jfieldID hero_race;     // race属性的id
    jfieldID hero_attack;   // attack属性的id
    jfieldID hero_agility;  // agility属性的id
} hero_struct;

```
接下来编写一个函数，它能够接收一个Hero对象。

```
void nativeInitHero(JNIEnv *env, jobject thiz, jobject hero, jstring name, jstring race) {
    if (hero == NULL){
        return;
    }
    // GetObjectClass()函数可以根据对象实例直接获取对象的class
    // 比FindClass()方便很多
    hero_struct.clazz = env->GetObjectClass(hero);
    if (hero_struct.clazz != NULL) {
        LOGE("Find %s class success!", "Hero");
        // 通过GetFeildID()获取Hero类的属性ID
        hero_struct.hero_name = env->GetFieldID(hero_struct.clazz, "name", "Ljava/lang/String;");
        hero_struct.hero_race = env->GetFieldID(hero_struct.clazz, "race", "Ljava/lang/String;");
        hero_struct.hero_attack = env->GetFieldID(hero_struct.clazz, "attack", "I");
        hero_struct.hero_agility = env->GetFieldID(hero_struct.clazz, "agility", "I");

        // 通过SetXXXField()函数，可以设置对象的属性值
        env->SetObjectField(hero, hero_struct.hero_name,  name);
        env->SetObjectField(hero, hero_struct.hero_race,  race);
        env->SetIntField(hero, hero_struct.hero_attack, 10);
        env->SetIntField(hero, hero_struct.hero_agility, 7);
    } else {
        return;
    }
}
```
需要说明的是，JNI默认只提供了8种基本类型的`SetXXXField()`函数，其它引用类型通过`SetObjectField()`设置即可。  

从上面的代码可以看出，在c++中，我们无法直接访问到Java类的属性，只能通过JNI获取类的属性的ID，然后再根据属性ID访问类的属性。

接下来注册该函数，这里会用到上面讲的动态注册。

```
// 一定要注意命名的精准性，否则就找不到这个函数了
{"initHero", "(Lcom/coorchice/cppdemo/entry/Hero;Ljava/lang/String;Ljava/lang/String;)V", (void *) nativeInitHero}
```
在Java中使用：

```
// 在JniHelper中声明native方法
public static native void initHero(Hero hero, String name, String race);

// 使用
Hero hero = new Hero();
initHero(hero, "恶魔猎手", "暗夜精灵");
hero.toString();
```
输出：

```
名字: 恶魔猎手
种族: 暗夜精灵
攻击力: 10
敏捷: 7
```


## 创建并返回自定义对象
直接看怎么在c++中创建自定义对象并返回它。我们只看最好用的一种方式。

```
// 定义Hero的路径宏，方便后面使用
#define HERO_PATH "com/coorchice/cppdemo/entry/Hero"
jobject nativeCreateHero(JNIEnv *env, jobject thiz, jstring name, jstring race)
{
    // 根据路径获取class
    jclass clz = env->FindClass(HERO_PATH);
    if (clz != NULL){
        LOGE("Find %s class success!", "Hero");
        // 获取Hero类的默认构造方法的ID
        // 后面需要使用这个ID来调用构造方法
        // <init> 就表示构造方法的名称
        // 第三个参数是构造方法的签名，签名格式和上面讲的一样
        jmethodID hero_construct_id = env->GetMethodID(clz, "<init>", "()V");
        // NewObject() 函数可以根据构造方法ID创建一个新的对象
        jobject hero = env->NewObject(clz, hero_construct_id);
        
        hero_struct.clazz = clz;
        // 通过GetFeildID()获取Hero类的属性ID
        hero_struct.hero_name = env->GetFieldID(hero_struct.clazz, "name", "Ljava/lang/String;");
        hero_struct.hero_race = env->GetFieldID(hero_struct.clazz, "race", "Ljava/lang/String;");
        hero_struct.hero_attack = env->GetFieldID(hero_struct.clazz, "attack", "I");
        hero_struct.hero_agility = env->GetFieldID(hero_struct.clazz, "agility", "I");
        
        // 通过SetXXXField()函数，可以设置对象的属性值
        env->SetObjectField(hero, hero_struct.hero_name,  name);
        env->SetObjectField(hero, hero_struct.hero_race,  race);
        env->SetIntField(hero, hero_struct.hero_attack, 99999);
        env->SetIntField(hero, hero_struct.hero_agility, 99999);
        
        // 返回一个在c++中创建的Java对象给调用native方法的地方
        return hero;
    } else {
        return NULL;
    }
}
```
注意，只要是引用类型的对象，我们只需要把返回类型设置为`jobject`就行，在native方法中再写成真实类型。  

同样，使用上面的动态注册，注册该函数。

```
{"createHero", "(Ljava/lang/String;Ljava/lang/String;)Lcom/coorchice/cppdemo/entry/Hero;", (void *) nativeCreateHero}
```
再次提醒，一定要保证命名的精准。  

看看如何在Java中使用吧。

```
// 在JniHelper中声明相应的native方法
public static native Hero createHero(String name, String race);

// 使用
createHero("巫妖王", "人族").toString();
```
输出：

```
名字: 巫妖王
种族: 人族
攻击力: 99999
敏捷: 99999
```
