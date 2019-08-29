
 [Android Gradle项目配置](https://developer.android.com/studio/build/)   
 
 ![Android项目构建流程](http://ogemdlrap.bkt.clouddn.com/build-process_2x.png)  
 
 如图所示，典型 Android 应用模块的构建流程通常依循下列步骤：

1. 编译器将您的源代码转换成 DEX（Dalvik Executable) 文件（其中包括运行在 Android 设备上的字节码），将所有其他内容转换成已编译资源（AAPT）。
2. APK 打包器将 DEX 文件和已编译资源合并成单个 APK。不过，必须先签署 APK，才能将应用安装并部署到 Android 设备上。
3. APK 打包器使用调试或发布密钥库签署您的 APK：
 1. 如果您构建的是调试版本的应用（即专用于测试和分析的应用），打包器会使用调试密钥库签署您的应用。Android Studio 自动使用调试密钥库配置新项目。
 2. 如果您构建的是打算向外发布的发布版本应用，打包器会使用发布密钥库签署您的应用。要创建发布密钥库，请阅读[在 Android Studio 中签署您的应用](https://developer.android.com/studio/publish/app-signing#studio)。
4. 在生成最终 APK 之前，打包器会使用 [zipalign](https://developer.android.com/studio/command-line/zipalign) 工具对应用进行优化，减少其在设备上运行时的内存占用。  

构建流程结束时，您将获得可用来进行部署、测试的调试 APK，或者可用来发布给外部用户的发布 APK。

