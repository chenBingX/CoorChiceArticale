# 在Android Studio NDK中打印log
## 示例
```
// 导入必须的android/log.h头文件
#include <android/log.h>

// 可选。定义方便使用的宏
#definde LOGE(...) __android_log_print(ANDROID_LOG_ERROR, "Tag", __VA_ARGS__)

int main(int argc, char *argv[])
{
    // 直接使用LOGE宏打印
    LOGE("I'm loge!");
}
```
## log类型
在android/log.h定义有：

```
typedef enum android_LogPriority {
  ANDROID_LOG_UNKNOWN = 0,
  ANDROID_LOG_DEFAULT, /* only for SetMinPriority() */
  ANDROID_LOG_VERBOSE, //logv
  ANDROID_LOG_DEBUG,   //logd
  ANDROID_LOG_INFO,    //logi
  ANDROID_LOG_WARN,    //logw
  ANDROID_LOG_ERROR,   //loge
  ANDROID_LOG_FATAL,
  ANDROID_LOG_SILENT, /* only for SetMinPriority(); must be last */
} android_LogPriority;
```  

本文已在版权印备案，如需转载请访问版权印79745076
