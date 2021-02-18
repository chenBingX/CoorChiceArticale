

# 运行模式

- Debug 模式

- Profile 模式

- Release 模式

# Dump 出 Widget Tree

`debugDumpApp()`

```
import 'package:flutter/material.dart';

void main() {
  runApp(
    MaterialApp(
      home: AppHome(),
    ),
  );
}

class AppHome extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Material(
      child: Center(
        child: TextButton(
          onPressed: () {
            debugDumpApp();
          },
          child: Text('Dump App'),
        ),
      ),
    );
  }
}
```

# Dump 出 RenderTree

`debugDumpRenderTree()`


# Dump 出 LayerTree

`debugDumpLayerTree()`

# 任务执行信息

```
debugPrintBeginFrameBanner = true;

debugPrintEndFrameBanner = true;
```

或者

```
debugPrintScheduleFrameStacks = true;
```
