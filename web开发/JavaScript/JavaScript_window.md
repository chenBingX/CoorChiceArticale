


# 弹窗事件

## 警告框

```
window.alert("警告框")；
```

## 确认框

```
var r = window.confirm("按下按钮");
if(r==true)
{
}
else
{
}
```

## 提示框输入框

```
var r = window.prompt("请输入你的名字", "Harry Potter");
console.log("r = " + r);

```

# Screen

- screen.availWidth：可用的屏幕宽度
- screen.availHeight:：可用的屏幕高度


# window 计时器

通过 `setInterval()` 函数可以定时的循环执行逻辑。

```
function startRun() {
        window.setInterval(function () {
            var child = document.createElement("p");
            var text = document.createTextNode("一条数据");
            child.appendChild(text);
            document.body.appendChild(child);
        }, 1000);
    }

window.setInterval(startRun(), 1000);
```