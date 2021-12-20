# Kotlin基础



### 表示和处理选择：枚举和“when“

1. 在一个 when 分支上合并~个选项，使用 ","







# 函数的定义和调用



# 4. 类、对象和接口

## 4.1 定义类继承结构

### 4.1.1 接口

1. **申明和实现接口**

   * Kotlin 在类名后面使用冒号来实现继承，一个类可以实现任意多个接口，但是只能继承一个类。

   * override 修饰符用来标注被重写的父类或者接口的方法和属性，并且是强制要求的。

     ```ko
     interface Clickable {
         fun click()
     }
     ```

     ```ko
     class Button: Clickable {
         override fun click() = println("I was clicked")
     }
     ```

     

   * 接口的方法可以有一个默认实现，Kotlin没有特殊的注解：只需要提供一个方法体。而Java8 中需要在接口实现上标注 default 关键字。

     ![image-20211220115836557](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\image-20211220115836557.png)

   * 继承两个接口，并且接口中相同的方法，则使用需要把基类的名字放入到尖括号中进行调用 `super<Clickable> showOff （）`



### 4.1.2 open final abstract 修饰符：默认为 final

1. **open 与 final**

   Kotlin中的类和方法默认是final的，如果需要创建父类，则需要使用open修饰符来标示这个类，需要给每一个可以被重写的属性或方法添加 open 修饰符。

   

2. **abstract**

   abstract申明抽象类，抽象类必须包含一些没有实现但子类必须实现的抽象成员。抽象成员始终是open的，所以不需要显示地使用open修饰符

   

3. **类中访问修饰符的含义**（一图概括）

   ![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\类中访问修饰符的意义.jpg)



### 4.1.3 可见性修饰符：默认为public

![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\可见性修饰符.jpg)



### 4.1.4 内部类和嵌套类：默认是嵌套类

1. Kotlin 中没有显式修饰符的嵌套类与 Java 中的 static 嵌套类是一样的，要变成一个内部类来持有一个外部类的引用的话需要使用 inner 修饰符。

   | 类A在另一个类B中声明         | 在Java中       | 在Kotlin中    |
   | ---------------------------- | -------------- | :------------ |
   | 嵌套类（不存储外部类的引用） | static class A | class A       |
   | 内部类（存储外部类的引用）   | class A        | inner class A |

2. 在kotlin中内部类访问外部类的方式，需要使用 this@Outer从Inner 类去访问 Outer 类：

   ```kotlin
   class Outer{
       inner class Inner {
           fun getOuterReference(): Outer = this@Outer
       }
   }
   ```

   

### 4.1.5 密封类（Sealed 类）：定义受限的类继承结构

Sealed 类（密封类）用于对类可能创建的子类进行限制，用 Sealed 修饰的类的**直接子类**只允许被定义在 Sealed 类所在的文件中（密封类的间接继承者可以定义在其他文件中），这有助于帮助开发者掌握父类与子类之间的变动关系，避免由于代码更迭导致的潜在 bug。





## 4.2 声明一个带非默认构造方法或属性的类

### 4.2.1 初始化类：主构造方法和初始化语句块

1. **主构造方法的申明与参数初始化**

   * 原始方式

     **constructor 关键字**用来开始一个主构造方法或从构造方法的声明。 **init 关键字**用来引入一个初始化语句块。这种语句块包含了在类被 建时执行的代码并会主构造方法一起使用。为主构造方法有语法限制，不能包含初始化代码，这就是为什么要使用初始化语句块的原因。

     ```kotlin
     class User constructor(_nickname: String) {  // 带一个参数的柱构造方法
         val nickname:String
         
         init {  // 初始化语句块
             nickname = _nickname
         }
     }
     ```

     

   * 如果主构造方法没有注解或可见性修饰符，可以去掉constructor 关键字

     ```kotlin
     class User(_nickname: String) {
         val nickname = _nickname
     }
     ```

     

   * 如果属性用相应的构造方法参数数来初始化，代码可以通过把 val 关键字加在参数前的方式来进行简化。

     ```kotlin
     class User(val nickname: String)
     ```

     

2. **构造方法参数声明一个默认值：**

   ```kotlin
   class User (val nickname: String, val isSubscribed : Boolean = true)
   ```

   > 注意：如果所有的构造方法参数都有默认值，编译器会生成一个额外的不带参数的构造方法来使用所有的默认值 这可以让 Kotlin 使用库时变得更简单，因为可以通过无参构造方法来实例化类



3. **子类构造函数调用父类构造函数方法**

   ```kotlin
   open class User (val nickname: String) { . . . } 
   
   class TwitterUser(nickname: String) : User(nickname) { . . . }
   ```

   

4. **默认构造方法（未提供构造方法的时候）**

   * 如果没有给一个类声明任何的构造方法，将会生成一个不做任何 情的默认造方法。

     ```kotlin
     open class Button
     ```

   * 如果子类没有提供任何构造方法，必须显式地调用父类的构造方法，即便没有任何的参数。 

     ```kotlin
     class RadioButton: Button()
     ```

     注意与接口的区别： 接口没有构造方法，所以在你实现一个接口的时候，不需要在父类型列表中它的名称面再加上括号。

     

5. **私有化构造方法（禁止实例化类）**

   ```kotlin
   class Secretive private constructor() {}
   ```



### 4.2.2 构造方法：用不同的方式来初始化父类

> 不要声明多个从构造方法用来重载和提供参数 默认值 取而代之的是，应该直接标明默认值。

* 从构造方法

  从构造方法使用 constructor 关键字引出。只要需要，可以声明任意多个从构造方法。

* 调用父构造方法，使用 super()关键字

  

* 调用自己的构造方法，使用this()关键字





### 继承

在 kotlin 中所有类都有一个共同的超类 **Any** ，对于没有超类声明的类来说它就是默认超类。需要注意的是， Any 并不是 **java.lang.Object** ，它除了 **equals() 、 hashCode() 与 toString()** 外没有其他属性或者函数







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

![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\apply函数.jpg)





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

   ![image-20211211151202946](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\elvis运算符.jpg)



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

  