1. 通过 Android Studio 的 Debug 窗口中的 `Open DevTools` 可以在浏览器中打开 Flutter 的调试工具，功能强大。  
    - 关闭左上角的 Debug 标签
    - 查看性能
    - 选择组件
    - 查看组件边界

2. 如何引用一个特定的 **git** 分支/Tag 依赖？

    ```
    dependencies:
      project:
        git:
          url: git://github.com/munificent/kittens.git
          ref: some-branch （tag使用tag对应的标识码）
    ```


3. 混编时，flutter 工程的 android 项目不能为 application，需要在app的`build.gradle` 中修改：
    a. `apply plugin: 'com.android.application'` 改为 `apply plugin: 'com.android.library'`
    b. 将 **applicationId** 去掉
    c. 如果不想混编译了，把他们改回去

4. flutter 在 `pubspec.yaml` 中引入资源文件时，不支持多级目录，子目录也需要单独定义。 

``` 
    - lib
      - icon
        pic1.png
      pic2.png
```  

那么在 `pubspec.yaml` 中就需要定义: 

  ```
  flutter:
    asserts:
      - lib/
      - lib/icon/
  ```

5. 如果一个叫做 **library1** 的包有以下结构：
  
    ```
      - pubspec.yaml
      - lib
        - icon
          pic1.png
        pic2.png
    ```  

    当他被其它应用程序应用依赖时，想要 **library1** 包中使用了资源的地方能够展示，需要做以下几件事：  

    - **library1** 的 `pubspec.yaml` 中需要这样去注册资源：   

        ```  
        flutter:
          asserts:
            - packages/library1/pic2.png
            - packages/library1/icon/pic1.png
        ```  

    - **library1** 在引用自己的资源的时候，需要加包名：   

        ```
        new AssetImage('icon/pic1.png', package: 'library1')
        new AssetImage('pic2.png', package: 'library1')
        ```  


6. Text组件测量，文本长度测量

    ```
    var renderObject = (textPart.build(context) as RenderObjectWidget).createRenderObject(context);
    renderObject.layout(BoxConstraints());
    var size = renderObject.paintBounds.size;
    ```


7. 文字测量

     ```
     final counterInfoSpan = TextSpan(
             text: counterInfo,
             style: TextStyle(
               color: getTextColor(counterInfo),
               fontSize: 15,
               height: 1,
             ));
     final tp = TextPainter(
         text: counterInfoSpan, maxLines: 1, textDirection: TextDirection.ltr);
     tp.layout(maxWidth: 105.5);


     /// tp 包含了文字的必须要信息
     ```
