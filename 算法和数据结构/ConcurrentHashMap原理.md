# ConcurrentHashMap简要分析
ConcurrentHashMap中的数据结构是Segment，Segment中的数据结构是HashEntry，HashEntry的数据结构是key-value。  

# 内部数据结构分析
## HashEntry
类似于HashMap的Entry键值对。  

## Segment
Segment继承自ReentrantLock，这使得Segment可以充当锁的角色。在ConcurrentHashMap中，存在一个Segment的数组。  

![image](http://ogemdlrap.bkt.clouddn.com/concurrenthashmap3.jpg)  

在进行put操作时，是针对key的hash值对应的segment进行加锁，所以不会影响其它Segment的存储。这种机制称为`分离锁机制`，可以支持最多16个线程并发读写。