# Java知识体系

## 1. Java 基础



### 数据类型

- 知识点
  - [基本类型](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#基本类型)
  - [包装类型](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#包装类型)
  - [缓存池](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#缓存池)



### String

- [概览](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#概览)
- [不可变的好处](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#不可变的好处)
- [String, StringBuffer and StringBuilder](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#string-stringbuffer-and-stringbuilder)
- [String Pool](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#string-pool)
- [new String("abc")](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#new-stringabc)



### 运算

- [参数传递](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#参数传递)
- [float 与 double](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#float-与-double)
- [隐式类型转换](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#隐式类型转换)
- [switch](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#switch)



### 关键字

- [final](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#final)
- [static](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#static)



### Object 通用方法

* 知识点

  * [概览](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#概览)
  * [equals()](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#equals)
  * [hashCode()](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#hashcode)
  * [toString()](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#tostring)
  * [clone()](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#clone)

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



### 继承

- [访问权限](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#访问权限)
- [抽象类与接口](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#抽象类与接口)
- [super](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#super)
- [重写与重载](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#重写与重载)



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



### [异常](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#八异常)



### [泛型](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#九泛型)



### [注解](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#十注解)



## Java容器

### 综合知识

* 参考资料
  * [面试官: 我必问的容器知识点!](https://juejin.cn/post/6844904115001098254)
  * [Java集合类](https://blog.csdn.net/Siobhan/article/details/51455143)



### [概览](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 容器.md#一概览)

- [Collection](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 容器.md#collection)
- [Map](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 容器.md#map)



### List

* 知识点
  * ArrayList
    	1、有使用过 ArrayList 吗 ？说一下它的底层实现 ？
    	初始化大小？
    	如何自动增长的？
    	add和remove的流程是怎么样的？
    	ArrayList 在多线程使用应该注意什么?
    
  * LinkedList
  
* 有使用过 LinkedList 吗？说一下它的底层实现 ？
  
* LinkedList 实现 栈、列表 
      
    >  参考 [LinkedList实现栈、队列或者双端队列分析](https://blog.csdn.net/huangfan322/article/details/52756441)   |   [Java双向队列Deque栈与队列](https://blog.csdn.net/u013967628/article/details/85210036)
    
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
  
  * PriorityQueue 
  
    > [JAVA中PRIORITYQUEUE详解](https://www.cnblogs.com/Elliott-Su-Faith-change-our-life/p/7472265.html)



### Deque

 [Java双向队列Deque栈与队列](https://blog.csdn.net/u013967628/article/details/85210036)



## Java IO / NIO / NIO.2

### Java IO

* 知识点
  
  ![分类帮助记忆](https://user-gold-cdn.xitu.io/2018/5/14/1635daccc4d2bce3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
  
  * [一、概览](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#一概览)
  * [二、磁盘操作](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#二磁盘操作)
  * 三、字节操作
    - [实现文件复制](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#实现文件复制)
    - [装饰者模式](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#装饰者模式)
  * 四、字符操作
    - [编码与解码](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#编码与解码)
    - [String 的编码方式](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#string-的编码方式)
    - [Reader 与 Writer](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#reader-与-writer)
    - [实现逐行输出文本文件的内容](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#实现逐行输出文本文件的内容)
  * 五、对象操作
    - [序列化](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#序列化)
    - [Serializable](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#serializable)
    - [transient](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#transient)
  * 六、网络操作
    - [InetAddress](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#inetaddress)
    - [URL](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#url)
    - [Sockets](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#sockets)
    - [Datagram](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#datagram)
  
* 参考资料
  
  * [Java IO使用总结](https://blog.csdn.net/Siobhan/article/details/51306200)



### Java NIO

* 知识点
  * [流与块](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#流与块)
  * [通道与缓冲区](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#通道与缓冲区)
  * [缓冲区状态变量](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#缓冲区状态变量)
  * [文件 NIO 实例](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#文件-nio-实例)
  * [选择器](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#选择器)
  * [套接字 NIO 实例](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#套接字-nio-实例)
  * [内存映射文件](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#内存映射文件)
  * [对比](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#对比)

* 参考资料
  * [JDK10都发布了，nio你了解多少？](https://juejin.cn/post/6844903605669986317#heading-0)



### Java 文件 I/O(采用 NIO.2)

* 参考资料
  * [文件 I/O(采用 NIO.2) - Java Tutorials 中文版](https://pingfangx.github.io/java-tutorials/essential/io/fileio.html)



## Java  线程和并发

### 综合

* 参考资料
  * [Java并发编程（总结最全面的面试题！！！）](https://juejin.cn/post/6844904125755293710)



###  [使用线程](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#一使用线程)

* 知识点
  - [实现 Runnable 接口](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#实现-runnable-接口)
  - [实现 Callable 接口](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#实现-callable-接口)
  - [继承 Thread 类](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#继承-thread-类)
  - [实现接口 VS 继承 Thread](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#实现接口-vs-继承-thread)



### [基础线程机制](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#二基础线程机制)

* 知识点
  - [Executor](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#executor)
  - [Daemon](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#daemon)
  - [sleep()](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#sleep)
  - [yield()](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#yield)



### 中断

* 知识点

  - [InterruptedException](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#interruptedexception)
  - [interrupted()](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#interrupted)
  - [Executor 的中断操作](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#executor-的中断操作)

  

### [互斥同步](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#四互斥同步)
* 知识点

  - [synchronized](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#synchronized)
  - [ReentrantLock](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#reentrantlock)
  - [比较](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#比较)
  - [使用选择](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#使用选择)

### [线程之间的协作](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#五线程之间的协作)
* 知识点
  - [join()](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#join)
  - [wait() notify() notifyAll()](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#wait-notify-notifyall)
  - [await() signal() signalAll()](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#await-signal-signalall)

### [线程状态](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#六线程状态)
* 知识点

  - [新建（NEW）](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#新建new)
  - [可运行（RUNABLE）](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#可运行runable)
  - [阻塞（BLOCKED）](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#阻塞blocked)
  - [无限期等待（WAITING）](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#无限期等待waiting)
  - [限期等待（TIMED_WAITING）](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#限期等待timed_waiting)
  - [死亡（TERMINATED）](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#死亡terminated)

### [J.U.C - AQS](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#七juc---aqs)

* 知识点
  - [CountDownLatch](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#countdownlatch)
  - [CyclicBarrier](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#cyclicbarrier)
  - [Semaphore](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#semaphore)

### [J.U.C - 其它组件](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#八juc---其它组件)

* 知识点
  - [FutureTask](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#futuretask)
  - [BlockingQueue](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#blockingqueue)
  - [ForkJoin](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#forkjoin)

### [Java 内存模型](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#十java-内存模型)
* 知识点

  - [主内存与工作内存](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#主内存与工作内存)
  - [内存间交互操作](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#内存间交互操作)
  - [内存模型三大特性](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#内存模型三大特性)
  - [先行发生原则](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#先行发生原则)

### [线程安全](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#十一线程安全)
* 知识点

  - [不可变](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#不可变)
  - [互斥同步](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#互斥同步)
  - [非阻塞同步](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#非阻塞同步)
  - [无同步方案](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#无同步方案)

### [锁优化](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#十二锁优化)
* 知识点

  - [自旋锁](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#自旋锁)
  - [锁消除](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#锁消除)
  - [锁粗化](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#锁粗化)
  - [轻量级锁](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#轻量级锁)
  - [偏向锁](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#偏向锁)



### [十三、多线程开发良好的实践](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#十三多线程开发良好的实践)



### 线程池

* 知识点
  - 线程池的使用
  - 线程池的生命周期
  - 线程池的工作流程
  - ThreadPoolExecutor的参数说明
  - 线程池的拒绝策略



### 参考资料

  * [一篇简单易懂的Java多线程基础文章](https://mp.weixin.qq.com/s?__biz=MzA5MzI3NjE2MA==&mid=2650245349&idx=1&sn=9488549af471f3f0ee8a564a3c1516d8&chksm=8863798abf14f09cc5999fd7ff9b8af31f824266513da802aa3be3d2d6c41ca9d809a8714468&xtrack=1&scene=0&subscene=131&clicktime=1553648630&ascene=7&devicetype=android-28&version=2700033b&nettype=ctnet&abtest_cookie=AwABAAoACwATAAQAI5ceAFaZHgDAmR4A3JkeAAAA&lang=zh_CN&pass_ticket=WUZw9KLx3SitUmBAZTZsUEYBCJtQDNgkE%2BNF4RSSSU4%3D&wx_header=1)
  * [ThreadPoolExecutor 官方使用说明](https://blog.csdn.net/Siobhan/article/details/51282570?ops_request_misc=%7B%22request%5Fid%22%3A%22162195034216780271552440%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fblog.%22%7D&request_id=162195034216780271552440&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_v2~rank_v29-1-51282570.pc_v2_rank_blog_default&utm_term=线程池&spm=1018.2226.3001.4450)
  * [彻底搞懂Java线程池的工作原理](https://mp.weixin.qq.com/s/tyh_kDZyLu7SDiDZppCx5Q)







## Java 虚拟机

### 运行时的数据区域 | 内存结构

<img src="https://camo.githubusercontent.com/7318f015229248611d435ec944ded1d65b0bbd77acea11f855ebb79f1576a839/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f35373738643131332d386531332d346335332d623562662d3830316535383038306239372e706e67" alt="运行时的数据区域" style="zoom:50%;" />

* 知识点
  * 程序计数器（它也是内存区域中唯一一个不会出现OutOfMemoryError的区域）
  * Java虚拟机栈 （一句话便是：栈管运行，堆管存储）
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
  * 垃圾收集器
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
  * 双亲委派模型（好处？）
  * 自定义类加载器实现
* 参考资料
  * [Java虚拟机](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E8%99%9A%E6%8B%9F%E6%9C%BA.md)



## 学习资料

* [GitHub 星标 115k+的 Java 教程，超级硬核！](https://github.com/CyC2018/CS-Notes)
* 