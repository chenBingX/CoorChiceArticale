# 动态注册的优势
1. 由于是将函数映射表注册到JVM中，所以函数的调用速度更快。
2. 不用使用静态注册那套繁琐的命名规则。

# 动态注册示例

## 注册

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
## 解注册
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
# JNI描述符
前面在动态注册时，需要生成函数映射表，其中需要一个是`函数签名`，它是由JNI描述符来描述的，写错了函数就会找不到。

## 基本类型对应关系

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

## 引用类型描述符
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


## 数组描述符
数组描述符的格式为：`对应维度个[ + 类型描述符`。如：

```
// int[]
[I

// Object[][]
[[Ljava/lang/Object;

```

## 方法描述符
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


