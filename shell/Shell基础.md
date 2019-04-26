
> 所有操作都是在 MacOS 上进行

shell本身是一个命令解释器，介于操作系统的内核（kernel）态和用户态之间，可以执行系统调用及系统命令等，让用户以此来与操作系统实现互动。

# sh 分类

- sh，burne shell
- bash，burne again shell

通常 Linux 系统预设的都是 bash。

# 例子

```
# [#!]用来指定执行该脚本的解释器，后面的[/bin/bash]表明指定[/bin]目录下的[bash]程序来解释执行该脚本文件。
#!/bin/bash

# 用echo命令输出字符串"Hello World!"到终端显示器。
echo "Hello World!"
```

## 执行脚本文件

### 方法一

在 Terminal 中输入（需要进入到脚本文件所在目录）：

```
bash 脚本文件名.sh
```

### 方法二

先将文件属性改为可执行状态：

```
chmod +x 脚本文件名.sh

# 或者
chmod 777 hello_world.sh
```

在 Terminal 中输入（需要进入到脚本文件所在目录）：

```
./脚本文件名.sh
```

> 一般新建文件的默认属性是-rw-rw-r--，即644，不具有可执行属性x，
可使用chmod命令来改变文件属性（修改默认属性则使用umask命令），
例如将文件test.sh的属性改为可读可写可执行(rwx: 4 + 2 + 1 = 7)：

```
# Terminal 输入：ls -l 查看文件当前属性
ls -l test.sh

# 输出
-rwxrwxrwx 1 slot slot 0 12月 21 15:40 test.sh
```

# 基本语法

## 定义变量

和 Python 等脚本语言类似：

```
str="Hello world!"
```

⚠️注意，等号两边不能加空格。

## 引用变量：

```
echo $str
```

使用 `$`+[变量名] 引用变量。

## 设置只读变量

类似于 Java 中的 final。

```
readonly str
```

从此，变量 `str` 的值就不能再更改了。

## 删除变量：

```
unset str
```

## 条件语句

```
if [ condition1 ]
then
    command1
elif [ condition2 ]
    command2
else
    commandN
fi
```

注意⚠️，最后需要加 `fi` 表示条件结束。

```
case "${var}" in
    "$condition1" )
        command1
    ;;

    "$condition2" )
        command2
    ;;

    * )  # 这里相当于C中case语句的default
esac
```


注意⚠️，最后需要加 `esac` 表示 case 结束。

## 循环语句

```
for var in item1 item2 ... itemN
do
    command
done
```

注意⚠️，最后需要加 `done` 表示循环结束。

```
while [ condition ]
do
    command
done
```

注意⚠️，最后需要加 `done` 表示循环结束。


## 参数传递机制

Bash 使用 **位置参数** 机制。

```
# 定义函数
function func2()
{
    echo "This is func2"

    // 第一个参数
    a=$1
    // 第二个参数
    b=$2
    echo "a is : $a"
    echo "b is : $b"
}

# 调用函数
func2 "aaa" "bbb"
```

函数参数使用 `$1, $2, $3...` 表示，`$1` 是第一个参数, `$2` 是第 2 个参数...

两位数及以上的位置需要加 `{}` 如 `${10}`。

给一个脚本文件传递参数也是这个机制。

特殊参数：

|参数处理|说明|
|:--|:--|
|$0|该脚本文件的名字|
|$#|传递到脚本的参数个数|
|$*|以一个单字符串显示所有向脚本传递的参数。|
|$@|与$*相同，但是使用时加引号，并在引号中返回每个参数。|
|$$|脚本运行的当前进程ID号|
|$!|后台运行的最后一个进程的ID号|
|$-|显示Shell使用的当前选项，与set命令功能相同|
|$?|显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。|

## shift

用于参数左移。

通常用于在不知道传入参数个数的情况下依次遍历每个参数然后进行相应处理（常见于Linux中各种程序的启动脚本）。

```
#!/bin/bash
while [ $# != 0 ]
do
echo "prama is $1,prama size is $#"
shift
done
```

```
./shift_test.sh a b c
prama is a,prama size is 3
prama is b,prama size is 2
prama is c,prama size is 1
```

每次运行shift(不带参数的),销毁一个参数，后面的参数前移

## 数组

### 直接初始化

```
arr_name=(value1 value2 ... valueN)
```

### 下标初始化

```
arr_name[0]=value1
arr_name[1]=value2
arr_name[223]=value3 # 不连续初始化
```

### 访问数组元素

```
${arr_name[index]}
```

### 遍历数组

使用* 或@ 可以获取数组中的所有元素

```
my_arr=(A B C "ddd")

# 遍历数组格式1
echo "my_arr: ${my_arr[*]}

# 遍历数组格式2
echo "my_arr: ${my_arr[@]}
```

### 获取数组长度

在遍历数组前加一个 `#` 即可：

```
${#my_arr[*]}

${#my_arr[@]}
```

## 字符串

### 定义字符串

```
str1=hello
str2='hello'
str3="hello"
```

### 获取字符串长度

```
${#str}
```


### 字符串拼接


```
str1="hello"
str2="world"

str3=${str1}" "${str2}
```

### 提取字符串

```
# 在string中, 从位置position开始提取子串
${str:position}

# 在string中, 从位置position开始提取长度为length的子串
${str:position:length}
```

### 删除字符串

```
str="abcabcdefabcabc"

# 从str的开头,删除最短匹配的以a开头c结尾的子串
# 将删除最左端的abc
# 输出：abcdefabcabc
echo "${str#a*c}"

# 从str的开头,删除最长匹配以a开头b结尾的子串
# 将删除abcabcdefabcab
# 输出：c
echo "${str##a*b}"

# 从str的结尾,删除最短匹配以a开头c结尾的子串
# 将删除最右端的abc
# 输出：abcabcdefabc
echo "${str%a*c}"

# 从str的结尾,删除最长匹配以a开头c结尾的子串
# 将删除整个字符串
# 输出：空
echo "${str%%a*b}"
```

### 替换字符串

```
str="abcdefabc"

# 用hello替换第一个abc
echo ${str/"abc"/"hello"}    # 输出：hellodefabc

# 用hello替换第一个abc
echo ${str//"abc"/"hello"}   # 输出：hellodefhello

# 前缀匹配替换
echo ${str/#"abc"/"world"}   # 输出：worlddefabc

# 后缀匹配替换
echo ${str/％"abc"/"world"}   # 输出：abcdefworld
```

## 退出程序

```
# 正常退出
exit 0

# 异常退出，!=0 就行
exit 1
```
