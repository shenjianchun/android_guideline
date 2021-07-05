# Java知识体系

## 1. Java 基础



### Object 通用方法

* 知识点

  > ```java
  > public native int hashCode()
  > 
  > public boolean equals(Object obj)
  > 
  > protected native Object clone() throws CloneNotSupportedException
  > 
  > public String toString()
  > 
  > public final native Class<?> getClass()
  > 
  > protected void finalize() throws Throwable {}
  > 
  > public final native void notify()
  > 
  > public final native void notifyAll()
  > 
  > public final native void wait(long timeout) throws InterruptedException
  > 
  > public final void wait(long timeout, int nanos) throws InterruptedException
  > 
  > public final void wait() throws InterruptedException
  > ```

* 参考资料

  * [Java 基础 - Object通用方法](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E5%9F%BA%E7%A1%80.md#java-%E5%9F%BA%E7%A1%80)



### 反射

* 知识点
  * 什么是Class
  * 如何获取Class
  * 通过Class获取类修饰符和类型
  * Member
    	Field
    	Method
    	Constructor
  * 副作用
    	性能开销
    	安全限制
    	内部曝光
* 参考资料
  * [一篇文章带你学懂Java反射](https://mp.weixin.qq.com/s/PYjFA1v_mwMpyKACI3B9PQ)



## Java容器

### 综合知识

* 参考资料
  * [面试官: 我必问的容器知识点!](https://juejin.cn/post/6844904115001098254)
  * [Java集合类](https://blog.csdn.net/Siobhan/article/details/51455143)



### List

* 知识点
  * ArrayList
    	1、有使用过 ArrayList 吗 ？说一下它的底层实现 ？
    	初始化大小？
    	如何自动增长的？
    	add和remove的流程是怎么样的？
    	ArrayList 在多线程使用应该注意什么?
  * LinkedList 
    	有使用过 LinkedList 吗？说一下它的底层实现 ？
  * CopyOnWriteArrayList 
    	如何实现线程安全的
    	add和remove的安全实现方式
  * 对比
    	你在工作中对 ArrayList 和 LinkedList 是怎么选型的？
* 参考资料



### Map

* 知识点
  * HashMap 
  * ArrayMap
  * LinkedHashMap
  * TreeMap
  * ConCurrentHashMap
* 参考资料

### Queue

* 知识点
  * 队列和集合的相同点以及区别
  * LinkedBlockingQueue 
  * ArrayBlockingQueue 
  * 说一下 LinkedBlockingQueue 和 ArrayBlockingQueue 有什么区别?



## Java IO

* 知识点

* 参考资料
  * [Java IO使用总结](https://blog.csdn.net/Siobhan/article/details/51306200)



## Java NIO



## Java  线程和并发

### 综合

* 参考资料
  * [Java并发编程（总结最全面的面试题！！！）](https://juejin.cn/post/6844904125755293710)

    

### 线程

* 知识点
  * 使用线程
    * sleep()
    * yield()
    * 中断
  * 互斥同步
    * synchronized：同步代码块，使用对象和类
  * 线程池
    * ThreadPoolExecutor的参数说明
* 参考资料
  * [一篇简单易懂的Java多线程基础文章](https://mp.weixin.qq.com/s?__biz=MzA5MzI3NjE2MA==&mid=2650245349&idx=1&sn=9488549af471f3f0ee8a564a3c1516d8&chksm=8863798abf14f09cc5999fd7ff9b8af31f824266513da802aa3be3d2d6c41ca9d809a8714468&xtrack=1&scene=0&subscene=131&clicktime=1553648630&ascene=7&devicetype=android-28&version=2700033b&nettype=ctnet&abtest_cookie=AwABAAoACwATAAQAI5ceAFaZHgDAmR4A3JkeAAAA&lang=zh_CN&pass_ticket=WUZw9KLx3SitUmBAZTZsUEYBCJtQDNgkE%2BNF4RSSSU4%3D&wx_header=1)
  * [ThreadPoolExecutor 官方使用说明](https://blog.csdn.net/Siobhan/article/details/51282570?ops_request_misc=%7B%22request%5Fid%22%3A%22162195034216780271552440%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fblog.%22%7D&request_id=162195034216780271552440&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_v2~rank_v29-1-51282570.pc_v2_rank_blog_default&utm_term=线程池&spm=1018.2226.3001.4450)



### 并发

* 知识点
* 参考资料





## Java 虚拟机

### 运行时的数据区域 | 内存结构

* 知识点
  * 程序计数器
  * Java虚拟机栈
  * 本地方法栈
  * 堆
  * 方法区
  * 运行时常量池
  * 直接内存
* 参考资料
  * [大白话带你认识JVM](https://juejin.cn/post/6844904048013869064#heading-28)
  * [JVM之内存结构图文详解](https://www.huaweicloud.com/articles/4799df506d5b9ae8bd0a5cb5247723b5.html)
  * [Java虚拟机](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E8%99%9A%E6%8B%9F%E6%9C%BA.md)



### 垃圾收集

* 知识点
  * 判断一个对象是否可被回收
    * 引用计数算法
    * 可达性分析算法
    * 方法区的回收
    * finalize
  * 引用类型（强引用、软引用、弱引用、虚引用）
  * 垃圾收集算法
* 参考资料
  * [Java虚拟机](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E8%99%9A%E6%8B%9F%E6%9C%BA.md)



### 内存分配和回收策略

* 知识点
  * Minor GC 和 Full GC
  * 内存分配策略
  * Full GC 的触发条件
* 参考资料
  * [Java虚拟机](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E8%99%9A%E6%8B%9F%E6%9C%BA.md)

### 类加载机制

* 知识点
  * 类的生命周期
  * 类加载过程
  * 类初始化时机
  * 类与类加载器
  * 类加载器分类
  * 双亲委派模型
* 参考资料
  * [Java虚拟机](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E8%99%9A%E6%8B%9F%E6%9C%BA.md)



## 学习资料

* [GitHub 星标 115k+的 Java 教程，超级硬核！](https://github.com/CyC2018/CS-Notes)
* 