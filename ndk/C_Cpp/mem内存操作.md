# malloc(int n)
在堆内存上分配 n 个字节，返回一个 `void *` 指针类型。

# calloc(int n, int size)
在堆上，分配n*size个字节，并初始化为0，返回void* 类型

# realloc(void * p,int n)
重新分配堆上的void指针p所指的空间为n个字节，同时会复制原有内容到新分配的堆上存储空间。注意，若原来的void指针p在堆上的空间不大于n个字节，则保持不变。

# memset(s, c, size)
将s内存中，从当前位置开始，往后的size个字节内容用c替换。

# memcpy(d, s, size)
将s内存中，从当前位置开始，往后的size个字节内容拷贝到d内存中。

# memcmp(buf1, buf2, size)
比较buf1内存和buf2内存的前size个字节。大于返回正值，小于返回负值，相等返回0。


# free (void * p)
释放void指针p所指的堆上的空间。
