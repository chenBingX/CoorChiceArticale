1. 如果遇到某个三方库找不到的错误  
    删除 `flutter sdk/.pub-cache/hosted/` 下的文件，然后重新 `flutter packages get` 一下。  


2. Dart Error: Can't load Kernel binary: Invalid kernel binary: Indicated size is invalid.  
    可能是 Flutter 和 Dart 版本不一致。通过 `flutter upgrade` 更新，然后 **clean** 工程，再重新 **build**。


3. `flutter attach` 一直停留在 `Waiting for a connection from Flutter on *** ..`  
    这可能出现在混合工程中。
    a. 先将混合工程进程完全退出
    b. 执行 `flutter attach`
    c. 重新打开app，就能链接成功了


4. `Finished with error: Gradle build failed to produce an Android package.` 错误是由于flutter应用包名未指定
    在 **app** 下的 `build.gradle` 中指定 **applicationId** 就可以了  
    去掉的作用是让它能在原生 Android 工程中混编


5. 找不到 **flutter、dart、pub** 命令   
    打开 `.bash_profile` 添加路径，然后 `source .bash_profile`，重启命令行即可。
    ```
    export PATH="$PATH:/Users/coorchice/flutter/bin"
    
    export PATH="$PATH:/Users/coorchice/flutter/bin/cache/dart-sdk"

    export PATH="/Users/coorchice/flutter/bin/cache/dart-sdk/bin":$PATH
    ```

6. AS 打开 Flutter 工程，出现不可关闭的空白窗口。
    取消勾选 **Preferences.. -> Languages & Frameworks -> Flutter -> Enable code completion..**。然后重启。

