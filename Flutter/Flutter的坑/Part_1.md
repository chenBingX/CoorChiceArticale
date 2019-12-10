1. 如果遇到某个三方库找不到的错误  
    删除 `flutter sdk/.pub-cache/hosted/` 下的文件，然后重新 `flutter packages get` 一下。  


2. Dart Error: Can't load Kernel binary: Invalid kernel binary: Indicated size is invalid.  
    可能是 Flutter 和 Dart 版本不一致。通过 `flutter upgrade` 更新，然后 **clean** 工程，再重新 **build**。


3. `flutter attach` 一直停留在 `Waiting for a connection from Flutter on *** ..`  
    这可能出现在混合工程中。
    a. 先将混合工程进程完全退出
    b. 执行 `flutter attach`
    c. 重新打开app，就能链接成功了

    