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
