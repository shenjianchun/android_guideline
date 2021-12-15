# Kotlin基础



### 表示和处理选择：枚举和“when“

1. 在一个 when 分支上合并~个选项，使用 ","







# 函数的定义和调用



# 类、对象和接口

### 继承

在 kotlin 中所有类都有一个共同的超类 **Any** ，对于没有超类声明的类来说它就是默认超类。需要注意的是， Any 并不是 **java.lang.Object** ，它除了 **equals() 、 hashCode() 与 toString()** 外没有其他属性或者函数



### open final abs ract 修饰符：默认为 final

>  Java 类和方法默认是 open 的，而 Kotlin 中默认都是 final 的。如果你想允许创建 个类的子类，需要使用 open 修饰符来标示这个类。此外，需要给每一个可以被重写的属性或方法添加 open 修饰符。





### 数据类：自动生成通用方法的实现 - data修饰符

使用data修饰符的类，会自动覆写equals、 hashCode、toString。equals hashCode 方法会将所有在主构造方法中声明的属性纳入考虑。生成的 equals 法会检测所有的属性的值是否相等。 hashCode 方法会返回一个根据所有属性生成的哈希值。请注意没有在主构造方法中声明的属性将不会加入到相等性检查和哈希值计算中去。





### object 关键字：将声明一个类与创建一个实例结合起来

1. 创建单例

2. 伴生对象：工厂方法和静态成员的地盘 

   companion object

3. 实现匿名类





### 延迟初始化 by lazy



# Lambda编程



### 带接收者的lambda: with ”与“apply“

with 结构看起来像是 种特殊的语法结构，但它实际上是一个接收两个参数的函数：这个例子中两个参数分别是 stringBuilder lambda 这里利用了把 lambda 在括号外的约定，这样整个调用看起来就像是内建的语 功能 。当然你可以选择把它写成 with ( s tringBuilder, { . . . } ），但可读性就会差很多。

with 函数把它的第 个参数转换成作为第 个参数传给它的 lambda 接收者可以显式地通过 this 引用来访问这个接收者。或者，按照惯例，可以省略 this 引用，不用任何限定符直接访问这个值的方法和属性。



with 的返回值为lamda中的最后一行

![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\使用with构建字母表.jpg)



apply的返回值是接受者对象（作为实参传递给它的对象）





### lambda函数中的下划线 “_”

```
没有使用到的参数在 kotlin 中用"_"代替
```





# Kotlin类型系统





## 可空性

1. let函数

   let 函数让处理可空表达式变得更容易 和安全调用运算符一起，它允许你对表达式求值，检查求值结果是否为 null ，并把结果保存为一个变量。 所有这些

   作都在同 个简洁的表达式中。
   
   ![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\let函数.jpg)





2. Elvis 运算符：“? : " 

   Elvis 运算符（或者null 合并运算符）接收两个运算数，如果第一个运算数不为 null ，运算结果就是第一个运算数；如果第一个运算数为 null ，运算结果就是第二个运算数。

   ![image-20211211151202946](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\elvis运算符)



3. "let"函数



## 数组

1. 创建对象数组的方式
2. 基础类型的数组
3. 操作数组（遍历、转换）





## 基本数据类型和其他基本类型

###  Unit 类型： Kotlin 的“void ”



### Nothing 类型：“这个函数永不返回”





# 运算符重载和其他约定



### 使用委托属性：惰性初始化和 by lazy()

lazy 函数返回一个对象，该对象具有一个名为 getValue 且签名正确的方法，因此可以把它与 by 关键字一起使用来创建一个委托属性。 lazy 的参数是一个lambda ，可以调用它来初始化这个值。默认情况下， lazy函数是线程安全的，如果需要，可以设置其他选项来告诉它要使用哪个锁，或者完全避开同步，如果该类永远不会在多线程环境中使用。





# 阶函数： Lambda作为形参和返回僵

## 声明高阶函数

### 函数类型

声明函数类型，需要将函数参数类型放在括号中，紧接着是 个箭头和函数的返回类型



# 协程



# Kotlin开源项目

* [KotlinMvp](https://github.com/git-xuhao/KotlinMvp) ： 基于Kotlin+MVP+Retrofit+RxJava+Glide 等架构实现短视频类小项目，简约风格及详细注释





# 参考（综合）

* [《Kotlin实战》笔记](《Kotlin实战》.xmind)    

* [两万六千字带你Kotlin入门](https://github.com/leavesC/AndroidGuide/blob/master/kotlin_core/%E4%B8%A4%E4%B8%87%E5%85%AD%E5%8D%83%E5%AD%97%E5%B8%A6%E4%BD%A0Kotlin%E5%85%A5%E9%97%A8.md)

* [Kotlin 协程入门这一篇就够了](https://juejin.cn/post/6844903871655968781)

* [一文快速入门 Kotlin 协程](https://juejin.cn/post/6908271959381901325)

  