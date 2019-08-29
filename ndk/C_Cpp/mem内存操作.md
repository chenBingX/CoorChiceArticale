# memset(s, c, size)
将s内存中，从当前位置开始，往后的size个字节内容用c替换。

# memcpy(d, s, size)
将s内存中，从当前位置开始，往后的size个字节内容拷贝到d内存中。

# memcmp(buf1, buf2, size)
比较buf1内存和buf2内存的前size个字节。大于返回正值，小于返回负值，相等返回0。