# 防御式定义
防止被重复定义。

```
#ifndef KOTLINANDCPPDEMO_TEST_CPP1_H
#define KOTLINANDCPPDEMO_TEST_CPP1_H


class test_cpp1 {

};


#endif //KOTLINANDCPPDEMO_TEST_CPP1_H
```




# 异常处理
和Java等差不多。

```
try
{
    //可能抛出异常的逻辑
} catch (Exception e) 
{
    // 处理异常
}
```

# 动态分配内存之new
内存被分配到堆区域，必须手动释放。
在c++中，分别使用`new`和`delete`来为指针变量`申请`和`释放`内存。

```
int* new_a;
// 由于可能没有足够的空间来分配，所以可能申请失败
if (!(new_a = new int)) {
    exit(1); //正常退出程序
}
*new_a = 123; //在new_a指向的空间中存数据
cout<<"new_a的空间的地址为："<<new_a<<endl;
cout<<"new_a的空间中的值为："<<*new_a<<endl;
// 释放new_a指向的空间
delete new_a;
```
数组内存的分配：

```
char *new_chars = new char[20]; //申请一个一维数组
delete [] new_chars; //释放new_chars所占的空间
```

# 动态分配内存之malloc
内存被分配在自由内储存区，必须手动才能回收。

```
//分配128个（可根据实际需要替换该数值）整型存储单元，
//并将这128个连续的整型存储单元的首地址存储到指针变量p中
int *p;
p = (int*)malloc(sizeof(int) * 128);
```
释放：

```
free(p);
```


# 文件读取
ofstream和fstream打开可写的文件，ifstream打开可读的文件。使用open()函数来打开文件。

```
ofstream file;
file.open("path");
...
file.close();
```


# memcpy函数
内存拷贝。

```
char* s="GoldenGlobalView";
char d[20];
memcpy(d,s+12,4);//从第13个字符(V)开始复制，连续复制4个字符(View)
```


# snprintf()
将可变个参数(...)按照format格式化成字符串，然后将其复制到str中。  

若成功则返回欲写入的字符串长度，若出错则返回负值。

```
snprintf(接收，(赋值长度，可选)，格式化(可选)，对应参数......);
```

# typedef
相当于重命名。

```
typedef int int_32; //把int定义为int_32
```

# #和##

```
#define bs_new(type)        ((type##_t*)new_object(#type, sizeof(type##_t), type##_init, type##_destroy))

bs_new(http_res)；
```
展开：

```
(http_res_t*)new_object(http_res, sizeof(http_res_t), http_res_init, http_res_destory);
```

