

```
/// 修改分辨率
adb shell wm size 1080x1920






///
```

# Logcat

```
/// 将 log 输出到文件
adb logcat >1.txt


/// 清理 log
adb logcat -c
adb logcat --clear

/// 通过标签过滤
adb logcat -s 标签

/// 通过字符过滤
adb logcat | grep Wifi
```
