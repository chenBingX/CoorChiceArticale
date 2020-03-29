[使用 Flutter 构建 Web 应用](https://flutter.cn/docs/get-started/web)

[Build and release a web app](https://flutter.dev/docs/deployment/web)

[使用dhttpd预览本地web](https://pub.dev/packages/dhttpd)

[配置镜像地址](https://flutter.dev/community/china)

- 开启 Web 支持

```markdown
flutter config --enable-web
```


- 构建，然后在 chrome 上运行

```
flutter create .

flutter run -d chrome
```

- 如果 `flutter_web_sdk` 下不下来怎么办？

配置 flutter  下载环境为国内镜像：


```markdown
export PUB_HOSTED_URL=https://pub.flutter-io.cn

export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
```