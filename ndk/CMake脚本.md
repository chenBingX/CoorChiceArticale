# 警告警告！
使用CMake编译，extern的函数或变量，需要使用c编译。有的时候头文件间接导入会导致某些类型不对，出现函数未定义的错误。


# CMake脚本编写

## 指定版本号

```
# 指定最低版本号为3.4.1
cmake_minimum_required(VERSION 3.4.1)
```

## set 命令

`set` 用于定义（加赋值）。  

如

```
// 定义一个 A 变量
set(A)

// 定义一个 PATH 变量，并赋值为 ${PROJECT_SOURCE_DIR}
set(B ${PROJECT_SOURCE_DIR})
```

使用：
- 在非 if 中，使用 `${变量名}` 来引用
- 在 if 中，直接使用变量名

## add_library

```
add_library(自定义so库名 
            SHARED/STATIC
            c/c++文件路径(可以有多个)
            )
```
如：

```
add_library(native 
            SHARED
            src/main/cpp/NativeLoad.cpp
            src/main/cpp/NativeLoad2.cpp
            )
            
add_library(native-2
            SHARED
            src/main/cpp/base.cpp
            )
```

- SHARED 表示静态库
- STATIC 表示动态库

## find_library

```
find_library( 查路径

              想要查找的库名)
```
如：

```
# 在ndk的lib-log文件夹下查找log库
find_library(lib-log log)

# 在ndk的z-lib文件夹下查找z库
find_library(z-lib z)
```

## target_link_libraries

```
target_link_libraries(目标库名
                        
                        要关联的库名(可以有多个))
```
如：

```
target_link_libraries(native
                    ${lib-log} # 关联lib-log路径下找到的库
                    ${z-lib} # 关联z-lib路径下找到的库
                    )
                    
target_link_libraries(native-2
                    native # 关联native库。会隐式的关联${lib-log}和${z-lib}
                    )
```

## include_directories
包含指定路径下的所有头文件（不会包含子目录中的，需要单独写），这样头文件才可用。一个CMake文件有一个就行，表示所有library都这么引入。
```
include_directories(path1
                    path2
                    ...
                    )
```
如：

```
include_directories(src/main/cpp
                    src/main/cpp/me
                    src/main/cpp/me/android
                    )
```


## aux_source_directory和list 命令

```
# 查找src/main/cpp下的所有“c/cpp”文件，并将它们的完整路径存入SRC_LIST中
aux_source_directory(src/main/cpp SRC_LIST)
```

```
# 向SRC_LIST集合中添加src/main/cpp/2
list(APPEND SRC_LIST
    src/main/cpp/2
    )
```
使用：

```
add_library(native SHARED ${SRC_LIST})
```

## file 命令
- file(GLOB_RECURES variable [Relative path] [globbing expressions]...)
    **GLOB_RECURES** 能够查询指定目录及其子目录中的，符合条件的文件。  
    如：`file(GLOB_RECURES SOURCE "*.c")` 会匹配 CMakeList.txt 所在目录和其子目录中的 `.c`，然后存到 **SOURCES** 变量中。



## 定义标识符

```
# 定义了标识符__ANDROID__
set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -D__ANDROID__ -g")
```
在c/c++文件中就可以使用__ANDROID__了。

```
#ifdef __ANDROID__
...
#endif
```

## 配置so输出路径

```
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/../jniLibs/${ANDROID_ABI})
```
其中：
- ${PROJECT_SOURCE_DIR}表示项目的根目录，即app目录
- ..表示向上一级目录
- ${ANDROID_ABI}表示在gradle中设置的ABI版本，有几个就会生成几个文件  

build.gradle中：

```
android.defaultConfig{
        externalNativeBuild {
            cmake {
                abiFilters 'armeabi-v7a', 'arm64-v8a','armeabi'
            }
        }

    ndk {
            abiFilters 'armeabi-v7a', 'arm64-v8a',  'armeabi'
        }
}
```
则会生成'armeabi-v7a', 'arm64-v8a',  'armeabi'这3个版本的so文件。

## 关联导入的so库

```
add_library(Thirdlib SHARED IMPORTED ) # 添加第三方so库Thirdlib
set_target_properties(Thirdlib # 第三方库名
                    PROPERTIES IMPORTED_LOCATION # 指明本地导入
                    ${CMAKE_CURRENT_SOURCE_DIR}/jniLibs/${ANDROID_ABI}/libThirdlib.so # 第三方库路径
                    )
```
- ${CMAKE_CURRENT_SOURCE_DIR}表示CMakeLists.txt文件所在的路径
- ${ANDROID_ABI}表示ABI版本


```
include_directories( Thirdlib/include/ ) # 包含第三方库的头文件
```

# 内置路径变量

- `${CMAKE_SOURCE_DIR}`  
    CMakeList.txt 所在目录

- `${PROJECT_SOURCE_DIR}`
    和 `${CMAKE_SOURCE_DIR}` 等价

# 内置宏

- **CMAKE_LIBRARY_OUTPUT_DIRECTORY**
    指定 cmake 编译输出的路径

# 配置CMake环境
## 创建CMake文件
创建一个`CMakeLists.txt`文件。在build.gradle中指明路径。

```
android.externalNativeBuild{
    cmake {
            //这是和build.gradle同级目录直接写
            path "CMakeLists.txt"
        }
}
```


