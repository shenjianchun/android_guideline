# Kotlin基础







# 函数的定义和调用



# 类、对象和接口

## 继承

在 kotlin 中所有类都有一个共同的超类 **Any** ，对于没有超类声明的类来说它就是默认超类。需要注意的是， Any 并不是 **java.lang.Object** ，它除了 **equals() 、 hashCode() 与 toString()** 外没有其他属性或者函数



### open final abs ract 修饰符：默认为 final

>  Java 类和方法默认是 open 的，而 Kotlin 中默认都是 final 的。如果你想允许创建 个类的子类，需要使用 open 修饰符来标示这个类。此外，需要给每一个可以被重写的属性或方法添加 open 修饰符。







### object 关键字：将声明一个类与创建一个实例结合起来

1. 创建单例

2. 伴生对象：工厂方法和静态成员的地盘 

   companion object

3. 



# Lamada编程



# Kotlin类型系统



## 可空性

1. let函数

   let 函数让处理可空表达式变得更容易 和安全调用运算符一起，它允许你对表达式求值，检查求值结果是否为 null ，并把结果保存为一个变量。 所有这些

   作都在同 个简洁的表达式中。



2. Elvis 运算符：“? : " 

   Elvis 运算符（或者null 合并运算符）接收两个运算数，如果第一个运算数不为 null ，运算结果就是第一个运算数；如果第一个运算数为 null ，运算结果就是第二个运算数。

   ![image-20211211151202946](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\elvis运算符)



## 数组

1. 创建对象数组的方式
2. 基础类型的数组
3. 操作数组（遍历、转换）





# 运算符重载和其他约定





# 协程



# Kotlin开源项目

* [KotlinMvp](https://github.com/git-xuhao/KotlinMvp) ： 基于Kotlin+MVP+Retrofit+RxJava+Glide 等架构实现短视频类小项目，简约风格及详细注释





# 参考（综合）

* [《Kotlin实战》笔记](《Kotlin实战》.xmind)    

* [两万六千字带你Kotlin入门](https://github.com/leavesC/AndroidGuide/blob/master/kotlin_core/%E4%B8%A4%E4%B8%87%E5%85%AD%E5%8D%83%E5%AD%97%E5%B8%A6%E4%BD%A0Kotlin%E5%85%A5%E9%97%A8.md)

* [Kotlin 协程入门这一篇就够了](https://juejin.cn/post/6844903871655968781)

* [一文快速入门 Kotlin 协程](https://juejin.cn/post/6908271959381901325)

  