# 主要依赖函数
## dlopen

```
/**
 * 打开名为filename的so库，并返回一个so库的句柄，通过这个句柄可以操作so库
 * 如果，没有找到对应so库，返回NULL
 */
void* dlopen(const char* filename, int flag)
```
flag参数有两个值：  
- RTLD_LAZY：等需要时再解析符号。
- RTLD_NOW: 立即解析符号。

## dlsym

```
/**
 * 在handle句柄对应的so中找到名为symbol的函数，并返回函数指针
 * 没有找到，返回NULL
 */
void* dlsym(void* handle, const char* _Nonnull symbol)
```

# 实例

```
//使用map来缓存so库
map<string, void *> g_so_map;
char g_package_name[256] = {0};

jlong loadSo(JNIEnv *env, jclass jc, jstring so_name);
jint registJNIMethod(JNIEnv *env, jclass jc, jlong so, jstring jclassname, jstring jfunc, jstring jsignature);
static int registerNativeMethods(JNIEnv *env, const char *className, JNINativeMethod *gMethods, int numMethods);

jint JNI_OnLoad(JavaVM *vm, void *reserved) {  
    void *env = NULL;  
    //LOGI("JNI_OnLoad");  
    if (vm->GetEnv(&env, JNI_VERSION_1_6) != JNI_OK) {  
        return -1;
    }
  //通过获取进程来获取进程名
    FILE* fp = fopen("/proc/self/cmdline", "r");
    fread(g_package_name, sizeof(g_package_name), 1, fp);
    fclose(fp);

    g_jvm = vm;

    //注册两个基础函数，一个用来加载so库，一个用来加载函数
    JNINativeMethod method[2] = {
    	{"loadSo", "(Ljava/lang/String;)J", (void*)loadSo},
    	{"registJNIMethod", "(JLjava/lang/String;Ljava/lang/String;Ljava/lang/String;)I", (void*)registJNIMethod}
    	};
    	
  //以下操作为了获取基础包名前缀
  char* p_name_1 = strtok(g_package_name, ".");
  char* p_name_2 = strtok(NULL, ".");
  char loadClassName[sizeof(g_package_name)];
  //规定了创建com/xxxx/load/NativeLoad作为入口
  sprintf(loadClassName, "%s/%s/load/NativeLoad", p_name_1, p_name_2);
  registerNativeMethods((JNIEnv *) env, loadClassName, method, 2);

    return JNI_VERSION_1_6;
}

//加载so库
jlong loadSo(JNIEnv *env, jclass jc, jstring so_name){
	void *filehandle = NULL;  
    //获取so库名称
    const char* so = env->GetStringUTFChars(so_name, NULL);
    if(so == NULL) {  
       return 0;
    }

    map<string, void*>::iterator iter = g_so_map.find(string(so));
    if (iter == g_so_map.end())
    {   //动态加载so库。
        // RTLD_LAZY：等需要时再解析符号
        // RTLD_NOW: 立即解析符号
        //加载成功返回一个so库的句柄，下次通过个句柄就能直接操作so库了
        filehandle = dlopen(so, RTLD_LAZY);
        if (filehandle==NULL)
        {   //没找到，尝试从本地data目录加载

            //定义一个1k的字符数组
            char libpath[1024];
            //把本地so库路径写入libpath中
            snprintf(libpath, sizeof(libpath), "/data/data/%s/lib/%s",g_package_name, so);
            //动态加载so库。
            filehandle = dlopen(libpath, RTLD_LAZY);
            if (filehandle==NULL)
            {
                //还没加载到就返回
                return 0;
            }
        }
    //缓存加载到的so库句柄
        g_so_map[string(so)] = filehandle;
    }
    else{
        filehandle = iter->second;
    }

    env->ReleaseStringUTFChars(so_name, so);
  // 返回so库句柄
    return (jlong)filehandle;
}

//注册函数
jint registJNIMethod(JNIEnv *env, jclass jc, jlong so, jstring jclassname, jstring jfunc, jstring jsignature){
	const char* classname = env->GetStringUTFChars(jclassname, NULL);
	const char* java_func = env->GetStringUTFChars(jfunc, NULL);
	const char* signature = env->GetStringUTFChars(jsignature, NULL);
	//const char* native_func = env->GetStringUTFChars(jnative_func, NULL);
    if(classname==NULL || java_func == NULL || signature==NULL) {  
       return 0;
    }
  // 根据so库句柄，函数名来链接so库中对应的函数，并获得函数指针
    void* func = dlsym((void*)so, java_func);
    if (func==NULL)
    {
    //没找对应函数到直接返回
    return 0;
    }
  //定义一个JNINativeMethod
    JNINativeMethod method = {java_func, signature, func};
  //动态注册函数
    int st = registerNativeMethods(env, classname, &method, 1);

    env->ReleaseStringUTFChars(jclassname, classname);
    env->ReleaseStringUTFChars(jfunc, java_func);
    env->ReleaseStringUTFChars(jsignature, signature);
    //env->ReleaseStringUTFChars(jnative_func, native_func);

    return st;
}

//真正注册的地方
static int registerNativeMethods(JNIEnv *env, const char *className, JNINativeMethod *methods, int numMethods){  
    jclass clazz;  
    clazz = env->FindClass(className);  
    if (clazz == NULL) {  
        return JNI_FALSE;  
    }  
    //注册函数
    if (env->RegisterNatives(clazz, methods, numMethods) < 0) {  
        return JNI_FALSE;  
    }  
    return JNI_TRUE;  
}  
```
