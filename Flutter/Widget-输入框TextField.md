在 Flutter 中，使用 TextField 来创建一个输入框。  

# 1.TextField 的属性

|属性   |类型|说明|
|---|---|---|
|controller|TextEditingController|用于和输入框交互，获取输入内容|
|focusNode|FocusNode|用于控制TextField是否占有当前键盘的输入焦点|
|decoration|InputDecoration|用于控制TextField的外观显示|
|keyboardType|TextInputType|用于设置该输入框默认的键盘输入类型|
|textInputAction|TextInputAction|键盘动作按钮图标(即回车键位图标)|
|style|TextStyle|正在编辑的文本样式|
|textAlign|TextAlign|输入框内编辑文本在水平方向的对齐方式|
|textDirection|TextDirection|输入框内文字方向|
|autofocus|bool|是否自动获取焦点|
|obscureText|bool|是否隐藏正在编辑的文本，会用“•”替换内容|
|maxLines|int|输入框的最大行数，默认为1|
|maxLength|int|框文本的最大长度|
|maxLengthEnforced|bool|当超过最大长度时，是否可以继续输入(此时输入框会变红)|
|onChanged||输入框内容改变时的回调函数|
|onEditingComplete|(){}|输入完成时触发，不能接收输入内容|
|onSubmitted|(String){}|输入完成时触发，接收输入内容|
|inputFormatters||指定输入格式；当用户输入内容改变时，会根据指定的格式来校验|
|enabled|bool|输入框是否可用|
|cursorWidth|double|光标宽度|
|cursorRadius|Radius|光标圆角|
|cursorColor|Color|光标颜色|
|onTap|GestureTapCallback|当用户点击输入框时触发|  

看个例子：  

```
Column(
  children: <Widget>[
    TextField(
      autofocus: true,
      decoration: InputDecoration(
          labelText: "用户名",
          hintText: "用户名或邮箱",
          prefixIcon: Icon(Icons.person)
      ),
    ),
    TextField(
      decoration: InputDecoration(
          labelText: "密码",
          hintText: "您的登录密码",
          prefixIcon: Icon(Icons.lock)
      ),
      obscureText: true,
    ),
  ],
)
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/TextField1.png)  

# 2.TextField 特殊属性

## 2.1 TextInputType 键盘类型

TextInputType 用于指定特定类型的输入键盘。

|类型|说明|
|---|---|
|text|文本输入键盘|
|number|数字键盘|
|phone|电话号码输入键盘|
|emailAddress|会显示“@ .”的键盘|
|url|会显示“/ .” 的键盘|  

## 2.2 InputDecoration

用于配置输入框的外观。

|属性|类型|说明|
|---|---|---|
|icon|Widget|输入框前面的图标|
|prefixIcon|Widget|输入框区域前的图标|
|suffixIcon|Widget|输入框区域后的图标|
|labelText|String|输入框的标题|
|labelStyle|TextStyle|输入框的标题Style|
|hintText|String|输入框的提示|
|hintStyle|TextStyle|输入框的提示Style|
|errorText|String|输入框的错误提示|
|errorStyle|TextStyle|输入框的错误提示Style|
|counterText|String|输入框的字数计数器文字|
|counterStyle|TextStyle|输入框的字数计数器文字Style|
|contentPadding|EdgeInsetsGeometry|输入框的Padding|
|filled|bool|输入框输入区域是否填充颜色|
|fillColor|Color|输入框输入区域的填充颜色|
|border|InputBorder|输入框边框。默认是 UnderlineInputBorder，会有一条底边线。想要去掉，可以设置 InputBorder.none。如果想要加边框，可以使用 OutlineInputBorder。|


![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/InputDecoration.png)


## 2.3 TextEditingController

创建一个 TextEditingController：  


```
var controller = TextEditingController();
```

通过 TextEditingController 可以：  

1. 获得输入框内容  

    
    ```
    TextField(
        autofocus: true,
        controller: _controller, //设置controller
        ...
    );
    
    print(_controller.text);
    ```

2. 监听输入内容变化
    
    ```
    _controller.addListener((){
        print(_unameController.text);
      });
    ```
    
3. 操作输入框内容
    
    ```
    // 设置输入框内容
    _controller.text="hello world!";
    // 选中输入框内容
    _controller.selection=TextSelection(
        baseOffset: 2,
        extentOffset: _selectionController.text.length
    );
    // 清空输入框内容
    _controller.clear();
    ```
    
## 2.4 FocusNode

创建一个 FocusNode：  

```
var focusNode = FocusNode();
```

通过 FocusNode 可以操作 TextField 的焦点。  

```
// 请求焦点，唤起键盘
FocusScope.of(context).requestFocus(focusNode);
// 清除焦点，收起键盘
FocusScope.of(context).unfocus();
```

# 3.TextFormField

TextFormField 相比于 TextField，主要多了 `validator` 可以校验

```
TextFormField(
  ...
  validator: (v){
    // 校验
  },
)
```

