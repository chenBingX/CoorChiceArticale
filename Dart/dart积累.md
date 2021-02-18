
## 执行 Shell 命令

1. 依赖 `process_run` 插件

```
dependencies:
  process_run: ^0.11.1
```

2. 关闭 macOS 的安全配置

macos/Runner/DebugProfile.entitlements 和 macos/Runner/Release.entitlements

```
<dict>
	<key>com.apple.security.app-sandbox</key>
	<false/>
</dict>
```

3. 使用示例

```
import 'dart:async';

import 'package:process_run/shell.dart';

Future main() async {
  // This works on Windows/Linux/Mac

  var shell = Shell();

  await shell.run('''

# Display some text
echo Hello

# Display dart version
dart --version

# Display pub version
pub --version

  ''');

  shell = shell.pushd('example');

  await shell.run('''

# Listing directory in the example folder
dir

  ''');
  shell = shell.popd();
}
```
