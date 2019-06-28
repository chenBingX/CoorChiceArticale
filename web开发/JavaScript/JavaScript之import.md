
# import 用法

## 导入所有内容

```
import * from '/other.js';
```

## 导入指定内容

```
import {name1, name2} from '/other.js';
```

将 `/other.js` 中的使用 **export** 关键字修饰的 `name1` 内容和 `name2` 内容导入。

## 导入重命名

```
import {name1 as newName1, name2 as newName2} from '/other.js';
```

**as** 能够给导入内容重新命名。

## 执行一个 `.js` 文件

```
import '/other.js'
```

执行 `/other.js` 文件，而不是导入其内容。

# export 用法

## 使用 **export** 修饰

```
const name1 = 'xxx';
const fun = function(){};

export {name1, fun as name2};
```

对应的导入为：

```
import {name1, name2} from '/other.js';
```



## 使用 **export** 和 **default** 修饰。

```
export default function myFunction(){};

export default class People{}
```

对应的导入为：

```
import myFunction from '/other.js';

import People from '/other.js';
```

加上 **default** 之后，导入的时候就不用再加 `{}` 了。

