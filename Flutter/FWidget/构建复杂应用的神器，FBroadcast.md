
[![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)](https://github.com/Fliggy-Mobile)

> **FWidget** 用心提供精致的组件，助您构建精美的应用。


![](https://gw.alicdn.com/tfs/TB1IjopQUT1gK0jSZFrXXcNCXXa-1560-840.png)


**FWidget** 一直致力于为开发者们精心打造易于构建精美应用的 **Widget**。

迄今为止，**FWidget** 已经收到了来自开发者们的 **1215** 个 **Star**，感谢开发者们的支持，这对 **FWidget** 来说**至关重要**。

今天，**FWidget** 为开发者们带来了一个全新的组件 **FBroadcast**，以帮助开发者们能够更轻松的构建更为复杂精美的应用。

例如，在 **FWidget** 的官方网站 [**https://fwidget.cn**](https://fwidget.cn) 中，通过 **FBroadcast** 很轻松的就实现了成本极低的，且易于维护的**全局实时语言切换**。它十分简单有效和明确！

![](https://gw.alicdn.com/tfs/TB1atr.QUY1gK0jSZFMXXaWcVXa-720-511.gif)

# ✨ 特性

来看看 **FBroadcast** 为开发者提供了那些不可思议的能力支持：

- 支持**发送**和**接收**指定类型的消息

- 消息支持携带**任意类型**数据包

- 提供**环境注册**，一行代码即可移除环境内所有接收者

- 不可思议的**粘性广播**

- **双向通信**支持

- 易于构建简单明确的局部和全局状态管理


![](https://gw.alicdn.com/tfs/TB1mDbBGND1gK0jSZFsXXbldVXa-360-324.png)

# 🛸 传送区

#### 🛸 [【传送门：FBroadcast Github 主页】](https://github.com/Fliggy-Mobile/fbroadcast)

#### 📖 [【传送门：FBroadcast 文档】](https://pub.dev/documentation/fbroadcast/latest/fbroadcast/fbroadcast-library.html)

# 🍹 献给开发者的 FBroadcast 

**FBroadcast** 是一套高效灵活的**广播系统**，可以帮助开发者**轻松、有序**的构建具有极具复杂性的**关联交互**和**状态变化**的精美应用。


![](https://gw.alicdn.com/tfs/TB1xVcSe9R26e4jSZFEXXbwuXXa-1456-528.png)


**FBroadcast** 将为构建复杂的精美应用带来那些显而易见的改变呢？

- **Widget/模块间的完全解耦**  
 
    通过 **FBroadcast** 高效的广播系统，开发者可以轻易的完成**Widget/模块**的解耦。在应用构建的时候，经常需要 **Widget/模块A、B、C、..** 之间根据交互操作互相变更状态或数据，开发者们不得不为此让各个**Widget/模块互相依赖**或者为它们建立**统一的状态管理**，这能解决问题，但这让构建变得麻烦，也让变更变得难以进行。
    
    **FBroadcast** 通过建立起**简单、有效、明确**广播系统，使得在任意**Widget/模块**中**任意时刻/位置**的改变能够主动发出广播，而需要根据这些变更作出**响应或更新视图**的**Widget/模块**只需要注册相应的**信息接收器**，就可以在变更发生时，接收到消息，作出响应。这使得关联模块间不再需要互相依赖，或是为它们设计建立统一的状态管理器。
    
    **十分简单，轻量，和易于变更**。当一个**Widget/模块**不在需要根据另一个**Widget/模块**的变更而更新时，只需移除其中的**接收器**即可，而不用为此而大改**依赖关系**或是**状态管理器**。


- **简单、灵活、明确、易管理**  

    **FBroadcast**  为开发者提供了可以在任意时刻发送广播，和注册/移除接收器的能力，毫无约束和灵活。
    
    广播和接收器之间通过明确的类型（字符串）来互相确认身份，指定类型的广播，只能被指定类型的接收器接收。
    
    **FBroadcast** 提供了环境注册支持，开发者可以在环境解构时，通过 [unregister()] 函数一次性移除环境中的所有类型接收器，而无需记忆和关心究竟需要移除那些接收器。例如，开发者可以在 **Widget** 的 `dispose()` 中，将注册在该 **Widget** 中的所有接收器一次性全部移除。
    
    借助现代**IDEA**的能力，开发者可以为广播系统建立一张（或多张）统一的广播类型索引表，通过**IDEA**的**引用索引**，开发者可以轻松的、一目了然的看到该类型的广播在那些地方被发送过，在那些地方注册了接收器，十分易于管理和维护。而使用字符串来作为类型标识，使得开发者可以将不同类型的广播含义描述的足够清晰明白。
    

- **粘性广播支持**
    
    **FBroadcast** 提供了发送**粘性广播**的支持。在还没有注册任何接收器的情况下，开发者可以在事件发生时，预先发送一条**粘性广播**。**粘性广播**会被暂时滞留在广播系统中，当有接收器被注册时，即会立即广播。这有助于帮助开发者在做逻辑设计时采取更清晰有效的思路。
    
    例如，当一个控制模块中的开关按钮被打开，而此时开关所控制的模块还没有被构建，就可以先发送一条**粘性广播**，在模块被构建完成注册了接收器后，就会立即接收到**粘性广播**而进入开启状态（这与互相依赖、定义统一状态管理或是参数传递，然后检查开关状态的思路有本质区别）。


# 📡 这真不是一般的 Broadcast

## 🛠 Base Broadcast

> 通信就要简单，明确

通过 **FBroadcast** 来注册，发送广播非常简便。

```dart
/// 注册接收器
/// 
/// register
FBroadcast.instance().register(Key_Message, (value, callback) {
  /// do something
});

/// 发送消息
/// 
/// send message
FBroadcast.instance().broadcast(Key_Message);
```

**FBroadcast** 允许开发者在发送消息的时候，带有数据。

```dart
/// 注册接收器
/// 
/// register
FBroadcast.instance().register(Key_Message, (value, callback) {
  /// 获取数据
  /// 
  /// get data
  var data = value;
});

/// 发送消息和数据
/// 
/// send message and data
FBroadcast.instance().broadcast(
  /// 消息类型
  /// 
  /// message type
  Key_Message, 

  /// 数据
  /// 
  /// data
  value: data,
);
```

开发者可以选择将特定类型的消息进行持久化，这样就能轻易实现广播式的全局状态管理。

```dart
FBroadcast.instance().broadcast(
  /// 消息类型
  /// 
  /// message type
  Key_Message, 

  /// 数据
  /// 
  /// data
  value: data, 

  /// 将消息类型持久化
  /// 
  /// Persist the message types
  persistence: true,
);
```

当开发者将一个消息类型持久化后，就可以在任意位置，通过 `FBroadcast.value(String key)` 来获取广播系统中该类型消息的最新的数据。而更新广播系统中的数据只需要通过 `broadcast()` 即可完成。

> ⚠️注意，一个消息类型一旦持久化就只能通过 `FBroadcast.instance().clear(String key)` 来从广播系统中移除该类型的消息。

## 🧲 Sticky Broadcast

> 更多的选择，构建更精美的应用

**FBroadcast** 支持开发者发送**粘性广播**。

```dart
FBroadcast.instance().stickyBroadcast(
  /// 消息类型
  /// 
  /// message type
  Key_Message, 

  /// 数据
  /// 
  /// data
  value: data, 
);
```

当广播系统中没有对应类型的接收器时，**粘性广播** 将会暂时滞留在系统中，直到有该类型的接收器被注册，则会立即发出广播（当广播系统中有对应类型的接收器时，就和普通广播具有相同的表现）。

## ⛓ Two-way communication

> 双向沟通，双倍效率

**FBroadcast** 支持在广播发送点接收**接收器**返回的消息。

```dart
/// 发送消息
/// 
/// send message
FBroadcast.instance().broadcast(
  /// 消息类型
  /// 
  /// message type
  Key_Message, 

  /// 数据
  /// 
  /// data
  value: data, 
  
  /// 接收器返回的消息
  /// 
  /// The message returned by the receiver
  callback: (value){
    // do something
  }
);


/// 注册接收器
/// 
/// register
FBroadcast.instance().register(Key_Message, (value, callback) {
  /// 获取数据
  /// 
  /// get data
  var data = value;

  /// do something
  var result = logic();

  /// 返回消息
  /// 
  /// return message
  callback(result);
});
```

通过 **FBroadcast** 能够给十分轻松的实现双向通信。

## 🌏 Bind Context

> 一码卸载，快捷精准

**FBroadcast** 支持在注册接收器时传入一个**环境对象（可以是任意类型）**，这会将接收器注册到环境中，当环境解构时，开发者可以方便的一次性移除所有在该环境中注册的接收器。

```dart
/// 注册接收器
/// 
/// register
FBroadcast.instance().register(
  /// 消息类型
  /// 
  /// Message type
  Key_Message1,

  /// Receiver
  ///
  /// Receiver
  (value, callback) {
    /// do something
  },

  /// 更多接收器
  /// 
  /// more receiver
  more: {
    /// 消息类型： 接收器
    /// 
    /// Message type: Receiver
    Key_Message2: (value, callback) {
      /// do something
    },
    Key_Message3: (value, callback) {
      /// do something
    },
    Key_Message4: (value, callback) {
      /// do something
    },
  },

  /// 环境对象
  /// 
  /// context
  context: this,
);

/// 移除环境中的所有接收器
/// 
/// Remove all receivers from the environment
FBroadcast.instance().unregister(this);
```

# 👀 使用 FBroadcast 可以做些什么？

## 💫 消息传递

> **场景**：点击 **Start**，Runner 开始 Run，显示屏需要实时更新运动员的状态。

![](https://gw.alicdn.com/tfs/TB1ZgoHg_M11u4jSZPxXXahcXXa-1280-869.gif)

### 🏃 1. 创建 Runner: 
```dart
/// Runner
class Runner {
  Runner() {
    /// register
    FBroadcast.instance().register(Key_RunnerState, (value, callback) {
      if (value is String && value.contains("Run")) {
        /// receive start run message
        FBroadcast.instance().broadcast(Key_RunnerState, value: "0m..");
        run(20);
      }
    });
  }

  run(double distance) {
    /// send running message
    Timer(Duration(milliseconds: 500), () {
      FBroadcast.instance().broadcast(Key_RunnerState, value: "${distance.toInt()}m..");
      var newDistance = distance + 20;
      if (newDistance > 100) {
        FBroadcast.instance().broadcast(Key_RunnerState, value: "Win!\nTotal time is 2.5s");
      } else {
        run(newDistance);
      }
    });
  }
}
```

### 🍭 2. 创建 UI:

```dart
Column(
  mainAxisAlignment: MainAxisAlignment.center,
  children: <Widget>[
    Stateful(
      /// init
      initState: (setState, data) {
        FBroadcast.instance().register(
          Key_RunnerState,
          (value, callback) {
            /// refresh ui
            setState(() {});
          },
          /// bind context
          context: data,
        );
      },
      builder: (context, setState, data) {
        return FSuper(
          ...
          /// get running message
          text: FBroadcast.value(Key_RunnerState) ?? "Preparing..",
        );
      },
    ),
    const SizedBox(height: 100),
    FButton(
      text: "Start"
      ...
      onPressed: () {
        /// send start run message
        FBroadcast.instance().broadcast(Key_RunnerState, value: "Running...");
      },
    ),
  ],
)
```

在上面的示例中，通过 **FBroadcast** 简单清晰的实现了 Runner 和 UI 之间的通信。
  
> 1. 点击 Start 按钮，通过 **FBroadcast** 发送起跑消息给 Runner；

> 2. Runner 收到消息后，开始 Run，同时不断通过 **FBroadcast** 发出 Running info；

> 3. UI 由于注册了接收器，在接收到 Running info 时，通过 `FBroadcast.value()` 获取消息数据，自动更新视图。

整个过程中，Runner 和 UI 之间是**完全解耦**的，且 UI 只需在 `init` 中**注册接收器**（receiver 中调用 `setState((){})`），就能根据消息数据的变化，自动实时的更新视图，而无需开发者关心整个过程。

## ⛓ 双向通信

> **场景**：点击按钮请求定位，定位成功后接收结果，刷新定位点

![](https://gw.alicdn.com/tfs/TB18D6vQ.Y1gK0jSZFMXXaWcVXa-1280-951.gif)

### 📝 1. 全局定位服务提供商

```dart
class LocationServer {
  LocationServer() {
    init();
  }

  init() {
    /// register Key_Location receiver
    FBroadcast.instance().register(Key_Location, (value, callback) async {
      var loc = await location();

      /// return message
      callback(loc);
    });
  }

  /// Analog positioning
  Future<List<double>> location() async {
    await Future.delayed(Duration(milliseconds: 2000));
    return [Random().nextDouble() * 280, Random().nextDouble() * 150];
  }
}

```

### 📝 2. 点击发送定位请求，接收返回消息


```dart

FButton(
  ...
  text: "Location",
  onPressed: () {
    FLoading.show(context,
        color: Colors.black26, loading: buildLoading());
    /// request location
    FBroadcast.instance().broadcast(Key_Location,
        callback: (location) {
      /// The message returned by the receiver
      setState(() {
        FLoading.hide();
        this.location = location;
      });
    });
  },
)

```

**FBroadcast** 能够进一步简化需要双向通信的场景。开发者可以看到，在这个例子中，通过 **FBroadcast** 能够轻松的实现定位请求这种双向通信的场景，而且使得**定位服务提供商**和**UI**实现的完全的解耦。

**UI交互点**只需要发送定位请求的广播，任何注册该广播的**定位服务提供商**就可以接收该请求进行处理，然后返回结果到**UI交互点**。也就是说，随着项目的演进，开发者可以随时提供新的**定位服务提供商**，而无需关心任何的**UI**变更。

## 📱 局部状态管理


> **场景**：点击改变UI颜色

![](https://gw.alicdn.com/tfs/TB1jDjHQUY1gK0jSZFCXXcwqXXa-1280-869.gif)

### 📝 1. 点击发出事件

```dart
FButton(
  text: "Change Color",
  ...
  onPressed: () {
    /// send change color message
    FBroadcast.instance().broadcast(Key_Color, value: reduceColor());
  },
)
```

### 📝 2. UI 注册接收器

```dart
Stateful(
  /// init
  initState: (setState, data) {
    /// register
    FBroadcast.instance().register(
      Key_Color,
      (value, callback) {
        /// refresh ui
        setState(() {
        });
      },
      /// bind context
      context: data,
    );
  },
  builder: (context, setState, data) {
    return FSuper(
      ...
      /// get color value
      backgroundColor: FBroadcast.value<Color>(Key_Color) ?? mainBackgroundColor,
    );
  },
)
```

通过 **FBroadcast** 可以很轻易的完成 UI 交互之间的局部状态更新。上面的示例展示了颜色的变更，数据对象只有一个参数，实际开发过程中，开发者可以根据需要将通信的数据对象进行丰富扩展。

开发者只需要在需要更新 UI 的 Widget 中**注册接收器**，调用一次 `setState((){})`，在交互点发出消息。而不用去主动的将触发逻辑和 `setState((){})` 在所有的交互点都写一次。


## 💻 全局状态管理

> **场景**：点击头像跳转登陆页，当账号密码不为 null 时，登陆按钮才可以点击。点击登陆按钮发送登陆请求，登陆成功后，返回上一页，刷新用户信息。

![](https://gw.alicdn.com/tfs/TB1kOvGQKH2gK0jSZJnXXaT1FXa-1280-869.gif)

### 📝 1. 用户信息Widget注册接收器

```dart
class Avatar extends StatefulWidget {
  @override
  _AvatarState createState() => _AvatarState();
}

class _AvatarState extends State<Avatar> {

  User user;
  int msgCount = 0;
  @override
  void initState() {
    super.initState();
    FBroadcast.instance().register(
      Key_MsgCount,
      /// register Key_MsgCount reviver
      (value, callback) => setState(() {
        msgCount = value;
      }),
      more: {
        /// register Key_User reviver
        Key_User: (value, callback) => setState(() {
          /// get value
          user = value;
        }),
      },
      /// bind context
      context: this,
    );
  }

  @override
  Widget build(BuildContext context) {
    return FSuper(
      ...
      backgroundImage: (user == null || _textIsEmpty(user.avatar)) ? null : AssetImage(user.avatar),
      redPoint: user != null && msgCount > 0,
      redPointText: msgCount.toString(),
      text: user != null ? null : "Click Login",
      onClick: user != null
          ? null
          : () => Navigator.push(context, MaterialPageRoute( builder: (context) => LoginPage())),
    );
  }

  @override
  void dispose() {
    super.dispose();
    /// remove all receivers from the environment
    FBroadcast.instance().unregister(this);
  }
}
```

登陆页中注册 `Key_User` 接收器，当接收到登陆消息时，取出其中的数据，刷新UI。

### 📝 2. 构建数据模型

```dart
class User{
  String name;
  String avatar;
  int messageCount = 0;
  String info;
}
```

### 📝 3. 构建逻辑处理对象

```dart
class LoginHandler {
  String _userName;
  String _password;

  /// set user name, check to see if login is allowed
  set userName(String v) {
    _userName = v;
    if (_textNoEmpty(_userName) && _textNoEmpty(_password)) {
      FBroadcast.instance().broadcast(Key_Login, value: true);
    } else {
      FBroadcast.instance().broadcast(Key_Login, value: false);
    }
  }

  /// set user password, check to see if login is allowed
  set password(String v) {
    _password = v;
    if (_textNoEmpty(_userName) && _textNoEmpty(_password)) {
      FBroadcast.instance().broadcast(Key_Login, value: true);
    } else {
      FBroadcast.instance().broadcast(Key_Login, value: false);
    }
  }

  /// login
  void login() {
    Timer(Duration(milliseconds: 1500), () {
      /// login success，send login success message —— Key_User
      FBroadcast.instance().broadcast(
        Key_User,
        value: User()
          ..avatar = "assets/logo.png"
          ..name = _userName
          ..info =
              "Seriously provide exquisite widget to help you build exquisite application.",
        /// Persistence Key_User
        persistence: true,
      );
    });
  }
}
```

将逻辑处理转移到 **LoginHandler** 中进行隔离，所有的处理结果都通过 **FBroadcast** 广播出去，使注册到广播系统中的对应接收器能够响应。

### 📝 4. 登陆页

```dart
class LoginPage extends StatefulWidget {
  @override
  _LoginPageState createState() => _LoginPageState();
}

class _LoginPageState extends State<LoginPage> {
  /// Logic handler
  LoginHandler handler = LoginHandler();
  /// input controller
  FSearchController _controller1 = FSearchController();
  FSearchController _controller2 = FSearchController();

  @override
  void initState() {
    super.initState();
    _controller1.setListener(() {
      /// update userName
      handler.userName = _controller1.text;
    });
    _controller2.setListener(() {
      /// update password
      handler.password = _controller2.text;
    });
  }

  @override
  Widget build(BuildContext context) {
    return {
      ...
      /// userName input
      FSearch(
        controller: _controller1,
        ...
      ),
      ...
      /// userName input
      FSearch(
        controller: _controller2,
        ...
      ),
      ...
      Stateful(
        initState: (setState, data) {
          /// register login receiver
          FBroadcast.instance().register(
            Key_Login,
            /// refresh ui
            (value, callback) => setState(() {}),
            more: {
              /// register user receiver
              Key_User: (value, callback) {
                FLoading.hide();
                Navigator.pop(context);
              },
            },
            /// bind context
            context: data,
          );
        }, 
        builder: (context, setState, data) {
          return FButton(
            ...
            text: "LOGIN",
            /// Key_Login value=true is allowed to click login
            onPressed: !(FBroadcast.value(Key_Login) ?? false)
                ? null
                : () {
                    _controller1.clearFocus();
                    _controller2.clearFocus();
                    FLoading.show(context);
                    /// Execute login logic
                    handler.login();
                  },
          );
      },),
      ...
    };
  }
}
```

注册接收器时，只需在接收回调中调用 `setState((){})`，后续所有的数据变化刷新，开发者就可以不用关注了。而给 UI 赋值可以方便的通过 **FBroadcast.value()** 获取对应数据来进行。

> ⚠️注意，对于需要全局使用的状态/数据模型，它们对应的广播类型，在发送时，**需要至少有一次将 persistence** 设置为 **true**。上面示例中，就在登陆成功后，对 `Key_User` 类型的广播进行了持久化。

```dart
/// login success，send login success message —— Key_User
FBroadcast.instance().broadcast(
  Key_User,
  value: User()
    ..avatar = "assets/logo.png"
    ..name = _userName
    ..info =
        "Seriously provide exquisite widget to help you build exquisite application.",
  /// Persistence Key_User
  persistence: true,
);
```

上面的示例中展示了通过 **FBroadcast** 可以轻松快速的实现**消息传递**，进行 **局部、全局状态管理和刷新**，很好的将**各个模块，逻辑以及UI** 进行解耦。**FBroadcast** 提供了简洁易懂，而且十分灵活的广播系统，极少的束缚让开发者可以快速上手，轻松实现复杂逻辑的简化，帮助开发者构建出**易于维护的、复杂的、精美的**应用。

**FBroadcast** 在使用过程中，配合统一的**广播类型注册表**（也可以按模块分多张），开发者可以很轻易的借助 **IDEA** 的引用检索能力，随时查看所有广播的情况，对于不断迭代过程中的维护十分有益。

![](https://gw.alicdn.com/tfs/TB1eg6tdQcx_u4jSZFlXXXnUFXa-1280-442.gif)


### [想要了解更多详细内容？请访问 **FBroadcast** 官方主页 (PS：别忘了投出一个你认可的 **Star** 哦 😘)。](https://github.com/Fliggy-Mobile/fbroadcast)

# 😃 如何使用？

在项目 `pubspec.yaml` 文件中添加依赖：

## 🌐 pub 依赖方式

```
dependencies:
  fbroadcast: ^<版本号>
```

> ⚠️ 注意，请到 [**pub**](https://pub.dev/packages/fbroadcast) 获取 **FBroadcast** 最新版本号

## 🖥 git 依赖方式

```
dependencies:
  fbroadcast:
    git:
      url: 'git@github.com:Fliggy-Mobile/fbroadcast.git'
      ref: '<分支号 或 tag>'
```

> ⚠️ 注意，分支号 或 tag 请以 [**FBroadcast**](https://github.com/Fliggy-Mobile/fbroadcast) 官方项目为准。


[![](https://gw.alicdn.com/tfs/TB1Zn2BGUT1gK0jSZFrXXcNCXXa-320-289.png)](https://github.com/Fliggy-Mobile/fbroadcast)

#### [感觉还不错？请到 《FBroadcast》的 Github 主页投出您认可的一个 Star 🌟 吧！](https://github.com/Fliggy-Mobile/fbroadcast)

# 更多精彩组件

- [《FSuper》- 帮助开发者快速构建精美的复杂视图](https://github.com/Fliggy-Mobile/fsuper)

- [《FButton》- 为开发者准备了诸多美妙的配置项](https://github.com/Fliggy-Mobile/fbutton)

- [《FSwitch》- 具有优良交互和视效的精美开关元素](https://github.com/Fliggy-Mobile/fswitch)

- [《FRadio》- 一个适用于几乎任意单选场景的单选组件](https://github.com/Fliggy-Mobile/fradio)

- [《FFloat》- 满足你对浮动元素的一切想象](https://github.com/Fliggy-Mobile/ffloat)

- [《FRefresh》- 轻松构建下拉刷新效果](https://github.com/Fliggy-Mobile/frefresh)

- [《FDottedLine》- 辉煌的虚线效果](https://github.com/Fliggy-Mobile/fdottedline)

- [《FSearch》- 一应俱全的搜索框组件](https://github.com/Fliggy-Mobile/fsearch)

- [《FToast》- 精致灵活的 **Flutter** 原生 **Toast** 组件](https://github.com/Fliggy-Mobile/ftoast)

- [《FLoading》- 帮助开发者自由的使用 Loading](https://github.com/Fliggy-Mobile/floading)

