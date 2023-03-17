
# Java面试题


## 字符串
new一个String对象，它被存在哪?static String呢?final String呢?


## 容器
arraylist原理，为什么数组加transient，add和get时间复杂度，扩容原理，和linkedlist区别，原理，分别在什么场景下使用，为什么

transient是为了在序列化的时候使用自定义的序列化读写方法，因为arraylist底层数组可能没有装满，在自定义序列化的时候可以只把带元素的部分序列化


## Map
hashmap原理，put和get，为什么是8转红黑树，红黑树节点添加过程，什么时候扩容，为什么是0.75，扩容步骤，为什么分高低位，1.7到1.8有什么优化，hash算法做了哪些优化，头插法有什么问题，为什么线程不安全

HashMap底层数组是2的n次方

concurrenthashmap原理，put，get，size，扩容，怎么保证线程安全的，1.7和1.8的区别，为什么用synchronized，分段锁有什么问题，hash算法做了哪些优化


## 异常
怎么理解异常，它的作用是什么，你们工作中是怎么使用的