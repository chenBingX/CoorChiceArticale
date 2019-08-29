
# HashMap简介
HashMap是一种数组散列结构，一个HashMap包含一个数组，数组中的每个元素可以对应一个链表。  

![image](http://ogemdlrap.bkt.clouddn.com/hashmap1.jpg)  

# HashMap分析

创建HashMap的时候会创建一个HashMapEntry数组，默认HashMapEntry数组的大小为16，也可以自己设置。如果HashMap中的元素个数超过当前数组元素大小的0.75就会对HashMapEntry数组大小进行扩充。  

当存储一个`key-value`时，会运算key的哈希值hash，找到对应的数组中位置，如果该位置已经有值了就把值存在数组的该位置，如果该位置已经有值了，就用链表的方式往后存，组成链表。  

取值的时候同样通过计算出key的哈希值，获得对应的数组位置，然后遍历该位置的链表，使用equels来比较找到对应的值。  

# 哈希值是如何对应数组位置的呢？
当取得一个key的Hash值后，通过对HashMapEntry数组的长度取模的方式来获得对应的index。