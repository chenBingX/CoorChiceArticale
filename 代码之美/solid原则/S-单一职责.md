[参考地址](https://realm.io/cn/news/donn-felker-solid-part-1/)
- 一个类、一个方法只应该做它本职的工作，其余的逻辑处理应该放在其它合适的地方。通常抽取逻辑会使后期的维护变的更易进行。比如，在RecycleAdapter的onBindViewHolder()方法中，我们不应该处理除了绑定数据以外的其它逻辑，诸如计算、变换之类的逻辑应该转移或者抽离。
- 接口在确保一些类的单一原则时很有用，例如，当需要在列表数据中添加当数据为特定值时需要显示不同于一般的视图，那么把这层逻辑抽离到接口中，通过在别的地方实现，适配器来调用，达到适配器的单一化功能。