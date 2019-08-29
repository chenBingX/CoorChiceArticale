
# ArrayList简介
ArrayList内部是一个数组的数据结构，所以随机访问效率高，但是插入、删除效率低，因为涉及到整个数组的移动。  

因此，ArrayList更适合需要频繁取出元素的场景，对于需要频繁增删的场景就不适合。

# 基本操作的原理

## add(Object value)
在数组末尾添加元素，数组长度不够就扩容。  

## add(int index, Object value)
在数组指定位置添加元素，原来该位置的元素往后移动。  

## set(int index, Object value)
相当于替换指定位置的元素，会返回旧元素。 

## remove(int index)
删除指定位置的元素，会导致该位置之后的所有元素的移动。

## get(int index)
直接获取对应位置的元素。  

