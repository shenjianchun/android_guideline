# 2. Kotlin基础



## 2.1 基本要素

### 2.1.2 函数

1. 函数声明

![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\函数声明.jpg)

2. 表达式函数体

   如果函数体写在花括号中，我们说这个函数有代码块体。如果它直接返回了一个表达式，它就有表达式体。

   ```kotlin
   fun max(a : Int , b: Int) : Int = if (a > b) a else b
   ```

   

3. 注意，只有表达式体函数的返回类型可以省略。对于有返回值的代码块体函数，必须显式地写出返回类型和 return 语句。



### 2.1.3 变量

1. 可变变量和不可变变量

   * val（来自 value）一一 不可变引用。使用 val 声明的变量不能在初始化之后再次赋值。它对应的是 Java final 量。
   * var（来自variable）一一 可变引用。这种变量的值可以被改变。这种声明对应的是普通（非 final ）的 Java 量。

   >  默认情况下，应该尽可能地使用 val 关键字来声明所有的 Kotlin ，仅在必要的时候换成 var 。



### 2.1.4 字符串模板

1. "$" 字符

   ```kotlin
   fun main(args: Array<String>) {
       val name = if (args.size > 0) args[0] else "Kotlin"
       println("Hello, $name!")
   }
   ```

   ```kotlin
   fun main(args: Array<String>) {
       println("Hello, ${if (args.size > 0) args[0] else "someone"}!")
   }
   ```



## 2.2 类和属性



### 2.2.1 属性

![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\在类中申明可变属性.jpg)

1. 声明属性的时候，你就声明了对应的**访问器** （只读属性有一个getter，而可写属性既有getter也有setter）。 访问器的默认实现非常简单：创建

   一个存储值的宇段，以及返回值的getter和更新值的setter。



### 2.2.2 自定义访问器

```kotlin
class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
        get() {
            return height == width
        }
}

fun main(args: Array<String>) {
    val rectangle = Rectangle(41, 43)
    println(rectangle.isSquare)
}

```





## 2.3 表示和处理选择：枚举和“when“

### 2.3.1 声明枚举类

1. 申明一个枚举类

   ```kotlin
   enum class Color { 
   	RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
   }
   ```

   

### 2.3.2 使用 "when" 处理枚举类

1. when 是一个有返回值的表达式，因此可以写一个直接返回 when 表示式的表达式体函数。

2. when每个分支不用写 break，如果匹配成功，只有对应的分支会执行

   ```kotlin
   enum class Color {
       RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
   }
   
   fun getMnemonic(color: Color) =
       when (color) {
           Color.RED -> "Richard"
           Color.ORANGE -> "Of"
           Color.YELLOW -> "York"
           Color.GREEN -> "Gave"
           Color.BLUE -> "Battle"
           Color.INDIGO -> "In"
           Color.VIOLET -> "Vain"
       }
   
   fun main(args: Array<String>) {
       println(getMnemonic(Color.BLUE))
   }
   
   ```



### 2.3.3 在"when"结构中使用任意对象

1. 和Java中的swith不一样， "when"允许使用任何对象。

2. 在一个 when 分支上合并多个选项，使用 ","

   ```kotlin
   fun mix(c1: Color, c2: Color) =
           when (setOf(c1, c2)) {
               setOf(RED, YELLOW) -> ORANGE
               setOf(YELLOW, BLUE) -> GREEN
               setOf(BLUE, VIOLET) -> INDIGO
               else -> throw Exception("Dirty color")
           }
   
   fun main(args: Array<String>) {
       println(mix(BLUE, YELLOW))
   }
   ```

   



### 2.3.4 使用不带参数的"when"

```kotlin
fun mixOptimized(c1: Color, c2: Color) =
    when {
        (c1 == RED && c2 == YELLOW) ||
        (c1 == YELLOW && c2 == RED) ->
            ORANGE

        (c1 == YELLOW && c2 == BLUE) ||
        (c1 == BLUE && c2 == YELLOW) ->
            GREEN

        (c1 == BLUE && c2 == VIOLET) ||
        (c1 == VIOLET && c2 == BLUE) ->
            INDIGO

        else -> throw Exception("Dirty color")
    }

fun main(args: Array<String>) {
    println(mixOptimized(BLUE, YELLOW))
}
```



### 2.3.5 智能转换：合并类型检查和转换

1. 当检查过一个变量是某种类型，后面就不再需要转换它，可以就把它当作你检查过的类型使用。事实上编译器为你执行了类型转换，我们把这种行为称为智能转换。

   ![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\智能转换.jpg)

### 2.3.6 重构：用"when" 代替 "if"

```kotlin
fun eval(e: Expr): Int =
    when (e) {
        is Num ->
            e.value
        is Sum ->
            eval(e.right) + eval(e.left)
        else ->
            throw IllegalArgumentException("Unknown expression")
    }

fun main(args: Array<String>) {
    println(eval(Sum(Num(1), Num(2))))
}
```



### 2.3.7 代码块作为 "if" 和 "when" 分支

1. if 和 when 都可以使用代码块作为分支体。这种情况下，代码块中的最后一个表达式就是结果。

   ```kotlin
   fun evalWithLogging(e: Expr): Int =
       when (e) {
           is Num -> {
               println("num: ${e.value}")
               e.value
           }
           is Sum -> {
               val left = evalWithLogging(e.left)
               val right = evalWithLogging(e.right)
               println("sum: $left + $right")
               left + right
           }
           else -> throw IllegalArgumentException("Unknown expression")
       }
   
   fun main(args: Array<String>) {
       println(evalWithLogging(Sum(Sum(Num(1), Num(2)), Num(4))))
   }
   ```



## 2. 4 迭代事物："while" 循环 和 "for" 循环

### 2.4.1 "while" 循环

### 2.4.2 迭代数字：区间和数列

1. 区间， 区间本质上就是两个值之间的问隔，这两个值通常是数字一个起始值，一个结束值，使用" .. " 运算符。

   Kotlin 的区间是包含的或者闭合的，意味着第 个值始终是区间的 部分

   ```kotlin
   val oneToTen =  1 .. 10
   ```

2. 带步长的数列，使用 " step " 关键字

   ```kotlin
   for (i in 100 downTo 1 step 2)
   ```

3. until函数，创建半闭区间，不包含左区间

   ```kotlin
   for (x in 0 until size ）
   ```

   

### 2.4.3 迭代map

```kotlin
fun main(args: Array<String>) {
    val binaryReps = TreeMap<Char, String>()

    for (c in 'A'..'F') {
        val binary = Integer.toBinaryString(c.toInt())
        binaryReps[c] = binary
    }

    for ((letter, binary) in binaryReps) {
        println("$letter = $binary")
    }
}
```





### 2.4.4 使用"in"检查集合和区间的成员

1. 使用 in 运算符来检查一个值是否在区间中，或者它的逆运算 !n 来检查这个值是否不在区间中。

   ```kotlin
   fun recognize(c: Char) = when (c) {
       in '0'..'9' -> "It's a digit!"
       in 'a'..'z', in 'A'..'Z' -> "It's a letter!"
       else -> "I don't know…"
   }
   
   fun main(args: Array<String>) {
       println(recognize('8'))
   }
   ```

   

## 2.5 Kotlin中的异常

1. Kotlin throw 结构是一个表达式，能作为另一个表达式的一部分使用

2. "try" "catch" "finally"  , Kotlin中不需要throws语句，因为Kotlin 并不区分受检异常和未受检异常。

3. "try" 作为 表达式

   ```kotlin
   fun readNumber(reader: BufferedReader) {
       val number = try {
           Integer.parseInt(reader.readLine())
       } catch (e: NumberFormatException) {
           null
       }
   
       println(number)
   }
   
   fun main(args: Array<String>) {
       val reader = BufferedReader(StringReader("not a number"))
       readNumber(reader)
   }
   ```

   



# 3. 函数的定义和调用

## 3.1 在Kotlin中创建集合

Kotlin 没有采用它自己的集合类，而是采用的标准的 Java 集合类，主要方法有`hashSetOf` 、`arrayListOf` 、`hashMapOf` 等



## 3.2 让函数更好调用

### 3.2.1 命名参数

当调用一个 Kotlin 定义的函数时，可以显式地标明一些参数的名称。如果在调用一个函数时，指明了一个参数的名称，为了避免混淆，那它之后的所有参数都

需要标明名称。

```kotlin
joinToString(collectio口， separator =””, prefix =””, postfix =”·”)
```

> 不幸的是，当调用 Java 的函数时，不能采用命名参数，不管是JDK 中的函数，或者是 Android 框架的函数，都不行



### 3.2.2 默认参数值

在Kotlin 中，可以在声明函数的时候，指定参数的默认值，这样就可以避免创建重载的函数。**参数的默认值是被编码到被调用的函数中，而不是调用的地方。**

```kotlin
fun <T> joinToString(
    collection: Collection<T>, 
    separator: String =”,”, 
    prefix: String =””, 
    postfix: String = ””
): String
```





### 3.2.3 消除静态工具类：顶层函数和属性

1. 在包目录下创建 kt文件，在kt文件中创建的函数和属性便是顶层函数和属性。编译出来的Java文件就是对应的 public static 开头的函数和属性。

2. 使用 const属性可以 定义一个常量，编译成Java后便是 public static final 的属性。

   ```kotlin
   const val UNIX LINE SEPARATOR = "\"
   
   /* Java */ 
   public static final String UNIX_LINE_SEPARATOR = "\";
   ```

   

## 3.3 给别人的类添加方法：扩展函数和属性

扩展函数非常简单，它就是一个类的成员函数，不过定义在类的外面。**扩展函数不能访问私有的或者是受保护的成员**。

把要扩展的类或者接口的名称，放到即将添加的函数前面。这个类的名称被称为**接收者类型**；用来调用这个扩展函数的那个对象，叫作**接收者对象**

```kotlin
package strings 

fun String.lastChar(): Char = this.get(this.length - 1)

println("Kotlin".lastChar())
//String 就是接收者类型，而 Kotlin就是接收者对象
```



### 3.3.1 导入和扩展函数

```kotlin
// 导入单个函数
import strings.lastChar 
val c =”Kotlin". lastChar ()

// 用“*”导入
import strings.* 
val c =”Kotlin”. lastChar ()

// 使用关键字出来修改导入的类或者函数名称
import strings.lastChar as last 
val c =”Kotlin".last()
```



### 3.3.2 从Java中调用扩展函数

实质上，扩展函数是静态函数，它把调用对象作为了它的第一个参数。从 Java 中调用 Kotlin 的扩展函数变得非常简单：调用这个静态函数，然

后把接收者对象作为第 个参数传进去即可。

```kotlin
/* Java */ 
char c = StringUtilKt.lastChar (”Java");
```





### 3.3.3 作为扩展函数的工具函数

扩展函数无非就是静态函数的 个高效的语法糖。



### 3.3.4 不可重写的扩展函数

1. 扩展函数并不存在重写，因为 Koti in 会把它们当作静态函数对待。

2. 扩展函数并不是类的一部分，它是声明在类之外的。尽管可以给基类和子类都分别定义一个同名的扩展函数，但是具体调用哪一个，是由该变量的静态类型所决定的，而不是这个变量的运行时类型。

   ```kotlin
   fun View.showoff() = println("I'm a view!）
   fun Button.showoff() = println （"I'm a button!")
   
   >> val view: View = But ton() 
   >> view. showoff ()
   I'm a view!
   ```

   

### 3.3.5 扩展属性

1. 扩展属性提供了一种方法，用来扩展类的 API ，可以用来访问属性。但是扩展属性是没有地方存储的，需要定义getter 或 setter方法（setter方法取决于接收者类型的对象内容是否可变）

   ```kotlin
   package ch03.ex3_5_ExtensionProperties
   
   val String.lastChar: Char
       get() = get(length - 1)
   var StringBuilder.lastChar: Char
       get() = get(length - 1)
       set(value: Char) {
           this.setCharAt(length - 1, value)
       }
   
   fun main(args: Array<String>) {
       println("Kotlin".lastChar)
       val sb = StringBuilder("Kotlin?")
       sb.lastChar = '!'
       println(sb)
   }
   ```

   



## 3.4 处理集合：可变参数、中缀调用和库的支持

### 3.4.1 扩展Java集合的API

Kotlin定义了许多集合类的扩展函数，在 Kotli 标准库中都有声明。



### 3.4.2 可变参数：让函数支持任意数量的参数

1. **申明**

   Kotlin中的可变参数申明是在参数上使用 **vararg 修饰符**。

   ```kotlin
   fun listOf<T>(vararg values: T): St<T> { . . . }
   
   // 创建列表
   val list= list0f(2, 3, 5, 7, 11)
   ```

2. 当需要传递的参数己经包装在数组中时，使用 **展开运算符**

   ```kotlin
   fun main(args: Array<String>) {
       val list = listOf("args: ", *args)
       println(list)
   }
   ```

   

### 3.4.3 键值对的处理：中缀调用和解构声明 

1. 使用 mapOf 函数来创建 map

   ```kotlin
   val map= mapOf(l to "one" , 7 to "seven" , 53 to "fifty-there")
   ```

2. `to` 是特殊的函数调用，称为： **中缀调用**

   



## 3.5 字符串和正则表达式的处理

在Kotlin中使用正则表达式需要使用Regex 类型。



## 3.6 让你的代码更简洁：局部函数和扩展







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

  ```kotlin
  class MyButton: View {
      constructor(ctx: Context) 
      	: super(ctx) {
              //...
          }
      
      constructor(ctx: Context, attr:AttributeSet) 
      	: super(ctx, attr)  {
              //...
          }
  }
  ```

  

* 调用自己的构造方法，使用this()关键字

  ```kotlin
  class MyButton: View {
      constructor(ctx: Context) : this(ctx, MY_STYLE)
      
      constructor(ctx: Context, attr:AttributeSet) 
      	: super(ctx, attr)  {
              //...
          }
  }
  ```

  

### 4.2.3 字段访问器：getter 或 setter 

1. **自定义字段访问器**

   ```kotlin
   class Point(val x: Int, val y: Int) {
   
       val isEquals1: Boolean
           get() {
               return x == y
           }
   
       val isEquals2
           get() = x == y
   
       var isEquals3 = false
           get() = x > y
           set(value) {
               field = !value
           }
   
   }
   ```

2. **修改访问器的可见性**

   ```kotlin
   fun main() {
       val point = Point(10, 10)
       println(point.isEquals1)
       //以下代码会报错
       //point.isEquals1 = true
   }
   
   class Point(val x: Int, val y: Int) {
   
       var isEquals1: Boolean = false
           get() {
               return x == y
           }
           private set
       
   }
   ```

   



## 4.3 编译器生成的方法：数据类和类委托

### 4.3.1 通用对象方法

1. 字符串表示： toString()

2. 对象相等性： equals()

   > 在Kotlin 中，== 运算符是比较两个对象的默认方式：本质上说它就是调用 equals 来比较两个值的。因此，如果 equals 在你的类中被重写了，你能够很安全地使用 == 来比较实例。 要想进行引用比 ，可以使 === 算符，这与 Java 中的 ＝＝ 比较对象引用的效果一样

3. Hash 容器： hashCode() 

   > hashCode 方法通常与 equals 一起被重写。hashCode的契约：如果两个对象相等，它们必须有着相同的 hash p



### 4.3.2 数据类（data修饰符）：自动生成通用方法的实现

使用data修饰符的类，会自动覆写equals、 hashCode、toString。equals hashCode 方法会将所有在主构造方法中声明的属性纳入考虑。生成的 equals 法会检测所有的属性的值是否相等。 hashCode 方法会返回一个根据所有属性生成的哈希值。请注意没有在主构造方法中声明的属性将不会加入到相等性检查和哈希值计算中去。



### 4.3.3 类委托： 使用 “by” 关键字

类似于实现 装饰模式，下面是一个例子，编译器会生成并实现与Collection中相同的方法。需要通过复写这些方法来实现自己的逻辑。

```kotlin
class DelegatingCollection<T>( 
	innerList: Collection<T> = ArrayList<T>() 
) : Collection<T> by innerList {}
```

![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\使用类委托.jpg)



## 4.4 object 关键字：将声明一个类与创建一个实例结合起来

### 4.4.1 对象声明：创建单例

kotlin 中的对象声明被编译成了通过静态字段来持有它的单一实例的类，这个字段名字始终都是 INSTANCE。一个对象声明可以包含属性、方法、初始化语句块等的声明。唯一不允许的就是构造方法（包括主构造方法和从构造方法）。

```kotlin
class Test {

    object SingleClass {
        val names = arrayListOf<String>()
    }

    object SingleClass2 {
        val names = arrayListOf<String>()
    }

}

// 在Java中访问
public static void main(String[] args) {
   Test.SingleClass.INSTANCE.getNames();
   Test.SingleClass2.INSTANCE.getNames();
}
```



### 4.4.2 伴生对象（companion object）：工厂方法和静态成员的地盘

Kotlin 中的类不能拥有静态成员，Java static 关键字并不是 Kotlin语言的一部分。 作为替代， Kotlin 依赖包级别函数（在大多数情形下能够替代 Java 的静

方法）和 对象声明（在其 情况下替 Java 的静态方法，同时还包括静态字段〉，在大多数情况下，还是推荐使用顶层函数。

```kotlin
class A { 
	companion object { 
		fun bar() { 
			println (”Companion ob ject called")
         }
    }
}     
```

![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\工厂方法.jpg)



### 4.4.3 作为普通对象使用的伴生对象

// TODO



### 4.4.4 对象表达式：改变写法的匿名内部类

```kotlin
window.addMouseListener (
    object : MouseAdapter() {
        override fun mouseClicked(e: MouseEv ent ) {
            //...
        }
        
        override fun mouseEntered(e: MouseEvent) {
            //...
        }
    }
)
```



# 5. Lambda编程

## 5.1 Lambda表达式和成员引用

### 5.1.1 Lambda 简介：作为函数参数的代码块

1. lambda函数中的下划线 "  _ " ，没有使用到的参数在 kotlin 中用 " _ "代替



### 5.1.2 Lambda和集合

### 5.1.3 Lambda表达式的语法

![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\Lambda表达式语法.jpg)

1. Kotlin中的Lambda表达式始终在花括号包围。注意实参并没有用括号括起来。

2. 可以把 Lambda 表达式存储在一个变量中，把这个变量当作普通函数对待。

   ```kotlin
   fun main(args: Array<String>) {
       val sum = { x: Int, y: Int -> x + y }
       println(sum(1, 2))
   }
   ```

   

3. Kotlin 有这样一种语法约定，如果Lambda 表达式是函数调用的最后一个实参，它可以放到括号的外边。当Lambda 是函数唯一的实参时，还可以去掉调用代码中的空括号对。

   ```kotlin
   person.maxBy({p:Persion -> p.age})
   
   // 如果Lambda 表达式是函数调用的最后一个实参，它可以放到括号的外边
   person.maxBy(){p:Persion -> p.age}
   
   // 当Lambda 是函数唯一的实参时，还可以去掉调用代码中的空括号对。
   person.maxBy{p:Persion -> p.age}
   ```

4. 和局部变量一样，如果 Lambda 参数的类型可以被推导出来，就不需要显式地指定它。可以遵循这样一条简单的规则：**先不声明类型，等编译器报错后再指定它们**。

5. **默认参数名称 it**，如果当前上下文期望的是只有一个参数 lambda 且这个参数的类型可以推断出来，就会生成这个名称。仅在实参名称没有显式地指定时这个默认的名称才会生成。

   > 注意it 约定能大大缩短你的代码，但你不应该滥用它 尤其是在嵌套lambda 情况下，最好显式地声明每个 lambd 的参数。 否则，很难搞清楚it 引用的到底是哪个值。如果上下文中参数的类型或意义都不是很明朗，显式声明参数的方法也很有效。

   ![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\默认参数it.jpg)





### 5.1.4 在作用域中访问变量

1. lambda可以访问函数中的参数以及在lambda之前定义的局部变量。
2. 在 Kotlin 中不会仅限于访问 final 变量，lambda 内部也可以修改这些变量。
3. 默认情况下，局部变量的生命期被限制在声明这个变量的函数中 但是如果它被 lambda 捕捉了，使用这个变量的代码可以被存储并稍后再执行。



### 5.1.5 成员引用

1. 成员引用表达式使用 "::" 运算符来转换。

   ```kotlin
   val getAge = Person::age
   
   // 引用类函数或属性
   people.maxBy(Persion::age)
   
   // 引用顶层函数（不是类的成员），直接以 :: 开头
   fun salute() = println (” Salute ! ”)
   run(::salute)
   ```



## 5.2 集合的函数式API

### 5.2.1 基础：filter和map

1. filter 和 map 函数形成了集合操作的基础，很多集合操作都是借助它们来表达的。filter 函数遍历集合并选出应用给定 lambda 后会返回 true 的那些元素。

   ```kotlin
   fun main(args: Array<String>) {
       val list = listOf(1, 2, 3, 4)
       println(list.filter { it % 2 == 0 })
   }
   
   fun main(args: Array<String>) {
       val list = listOf(1, 2, 3, 4)
       println(list.map { it * it })
   }
   ```

2. 对map应用过滤和变换函数，filterKeys 和 mapKeys 过滤和变换 map的键，而另外的 filterValues mapValues 过滤和变换对 的值



### 5.2.2 "all"、"any"、"count"、"find"：对合集应用判断式

1. all 函数：是否集合中的所有元素都满足条件
2. any函数：集合中是否至少存在一个匹配的元素
3. count函数：集合中有多少个元素满足了判断式
4. find函数：在集合中找到第一个满足判断式的；没找到则返回null。find函数还有一个同义方法 firstOrNull 。



### 5.2.3 groupBy：把列表转换成分组的map



### 5.2.4 flatMap 和 flatten：处理嵌套集合中的元素

1. flatMap 函数做了两件事情：首先根据作为实参给定的函数对集合中的每个元素做变换（或者说映射），然后把多个列表合并（或者说平铺）成一个列表。

   ```kotlin
   fun main(args: Array<String>) {
       val books = listOf(Book("Thursday Next", listOf("Jasper Fforde")),
                          Book("Mort", listOf("Terry Pratchett")),
                          Book("Good Omens", listOf("Terry Pratchett",
                                                    "Neil Gaiman")))
       println(books.flatMap { it.authors }.toSet())
   }
   ```

   

2. flatten函数 ，你不需要做任何变换，只是需要平铺一个集合



## 5.3 惰性集合操作：序列

1. 和Java8 中的Stream类似
2. Kotlin惰性集合操作的入口就是 Sequence 接口。可以使用序列更高效地对集合元素执行链式操作，而不需要创建额外的集合来保存过程中产生的中间结果。
3. asSequence把集合转换成序列，toList做反向转换



### 5.3.1 执行序列操作：中间和末端操作

1. 序列操作分为：中间和末端操作。一次中间操作返回的是另一个序列，而一次末端操作返回的是一个结果。中间操作不会进行计算。



### 5.3.2 创建序列

1. generateSequence 函数。给定序列中 的前一个元素，这个函数会计算出下一个元素。

   ```kotlin
   fun main(args: Array<String>) {
       val naturalNumbers = generateSequence(0) { it + 1 }
       val numbersTo100 = naturalNumbers.takeWhile { it <= 100 }
       println(numbersTo100.sum())
   }
   ```

   这个例子中的 naturalNumbers numbersTolOO 都是有延期操作的序列。这些序列中的实际数字直到你调用末端操作（这里是 sum ）的时候才会求值。



## 5.4 使用Java函数式接口









## 5.5 带接收者的lambda: with ”与“apply“

### 5.5.1 "with" 函数

1. with 结构看起来像是一种特殊的语法结构，但它实际上是一个接收两个参数的函数：这个例子中两个参数分别是 stringBuilder和一个lambda。 这里利用了把 lambda 在括号外的约定，这样整个调用看起来就像是内建的语言功能 。当然你可以选择把它写成 with ( s tringBuilder, { . . . } ），但可读性就会差很多
2. with 函数把它的第一个参数转换成作为第二个参数传给它的 lambda 接收者。可以显式地通过 this 引用来访问这个接收者。或者，按照惯例，可以省略 this 引用，不用任何限定符直接访问这个值的方法和属性。
3. with 的返回值为lamda中的最后一个表达式的值

![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\使用with构建字母表.jpg)



apply的返回值是接受者对象（作为实参传递给它的对象）





### 5.5.2 "apply" 函数

1. apply 函数几乎和 with 函数一模一样， 唯一的区别是 apply 始终会返回作为实参传递给它的对象（换句话说，接收者对象） 。

   ![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\apply函数.jpg)

   

# 6. Kotlin类型系统

## 6.1 可空性

### 6.1.1 可空类型

1. "?"表示对象可以为 null， 没有问号的类型表示这种类型的变量不能存储 null 引用 。



### 6.1.2 类型的含义



### 6.1.3 安全调用运算符："?."

把null检查和一次方法调用合并成一个操作。

![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\安全调用运算符.jpg)





### 6.1.4 Elvis 运算符：“? : " 

Elvis 运算符（或者叫null 合并运算符）接收两个运算数，如果第一个运算数不为 null ，运算结果就是第一个运算数；如果第一个运算数为 null ，运算结果就是第二个运算数。

![image-20211211151202946](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\elvis运算符.jpg)



### 6.1.5 安全转换："as?"

"as？" 运算符尝试把值转换成指定的类型， 如果值不是合适的类型就返回 null,

![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\安全转换.jpg)

### 6.1.6 非空断言："!!"

非空断言使用双感叹号表示，可以把任何值转换成非空类型。如果对null 值做非空断言，则会抛出异常。

![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\非空断言.jpg)



### 6.1.7 "let"函数

let 函数让处理可空表达式变得更容易。和安全调用运算符一起，它允许你对表达式求值，检查求值结果是否为 null ，并把结果保存为一个变量。 所有这些

动作都在同一个简洁的表达式中。

![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\let函数.jpg)



### 6.1.8 延迟初始化的属性，"lateinit" 修饰符

延迟初始化的属性都是var ，因为需要在构造方法外修改它的值。而val 属性会被编译成必须在构造方法中初始化的 final 字段。

>  lateinit 属性常见的一种用法是依赖注入。在这种情况下，lateinit 属性的值是被依赖注入框架从外部设置的。

```kotlin
package ch06.ex1_8_2_LateinitializedProperties1

import org.junit.Before
import org.junit.Test
import org.junit.Assert

class MyService {
    fun performAction(): String = "foo"
}

class MyTest {
    private lateinit var myService: MyService

    @Before fun setUp() {
        myService = MyService()
    }

    @Test fun testAction() {
        Assert.assertEquals("foo",
            myService.performAction())
    }
}

```



### 6.1.9 可空类型的扩展

为可空类型定义扩展函数，可以允许接收者为 null 的（扩展函数）调用，并在该函数中处理 null ，而不是在确保变量不为null 之后再调用它的方法。



### 6.1.10 类型参数的可空性

1. Kotlin 中所有泛型类和泛型函数的类型参数默认都是可空的。任何类型，包括可空类型在内，都可以替换类型参数。

2. 要使类型参数非空，必须要为它指定一个非空的上界，那样泛型会拒绝可空值作为实参。

   ```kotlin
   // 为类型参数声明非空上界
   fun <T: Any> printHashCode(t: T) {
       println(t.hashCode())
   }
   ```



### 6.1.11 可空性和Java





## 6.2 基本数据类型和其他基本类型

### 6.2.1 基本数据类型：Int、Boolean及其他

Katlin 不区分基本数据类型和它们的包装类。对应到 Java 基本数据类型的类型完整列表如下：

* 整数类型 ：Byte、Short、 Int、 Long 
* 浮点数类型：Float、Double
* 字符类型：Char
* 布尔类型：Boolean

### 6.2.2 可空的基本数据类型：Int？、Boolean？及其他

Kotlin 中的可空类型不能用 Java 的基本数据类型表示，因为 null 只能被存储在Java 的引用类型的变量中。这意味着任何时候只要使用了基本数据类型的可空版

本，它就会编译成对应的包装类型。



### 6.2.3 数字转换

1. Kotlin必须显式的进行数字转换，Kotlin 不会自动地把数字从一种类型转换成另外一种。

   ```kotlin
   val i = 1
   val l:Long =i  // 编译错误
   
   val i = 1
   val l: Long ＝ i.toLong()
   ```

   

2. 每一种基本数据类型（ Boolean 除外）都定义有转换函数： toByte()、to Short()、 toChar()等。这些函数支持双向转换：既可以把小范围的类型括展到大范围，比如Int.toLong()，也可以把大范围的类型截取到小范围，比如Long.toInt() 

3. 基本数据类型字面值

   * 使用后缀 L 表示 Long 类型（长整型）字面值： 123L
   * 使用标准浮点数表示 Double （双精度浮点数）字面值： 0.12、2.0、1.2e10、1.2e-10
   * 使用后缀 F 表示Float类型（浮点数）字面值： 123.4f、.456F、 1e3f
   * 使用前缀 0x 或者0X 表示十六进制字面值： 0xCAFEBABE 或者 0xbcdL
   * 使用前缀 0b 或者 0B 表示二进制字面值： 0b000000101



### 6.2.4 "Any" 和 "Any?"：根类型

1. Any 类型是 Kotlin所有非空类型的超类型（非空类型的根），包括像 Int 这样的基本数据类型。可空类型则需要使用 Any?

2. 在底层，Any 类型对应 Java.lang.Object。Any 并不能使用其他Java.lang.Object 的方法（比如 wait、notify），但是可以通过手动把值转换成

   java.lang.Object 来调用这些方法。



### 6.2.5 Unit类型：Kotlin的“void”

1. Unit 是一个完备的类型，可以作为类型参数，而 void 却不行。只存在一个值是 Unit 类型，这个值也叫作 Unit ，并且（在函数中）会被隐式地返回。在重写返回泛型参数的函数时这非常有用，只需要让方法返回 Unit 类型的值。

   ```kotlin
   interface Processor<T> { 
   	fun process(): T
   }
   
   class NoResultProcessor : Processor<Unit> {
   	override fun process() {
           // do stuff
           // 这里不需要显示的返回
       }
   }
   ```

   

### 6.2.6 Nothing类型：“这个函数永不返回”

1. Nothing 类型没有任何值，只有被当作函数返回值使用，或者被当作泛型函数返回值的类型参数使用才会有意义。在其他所有情况下，声明一个不能存储任何

   值的变量没有任何意义

   ```kotlin
   fun fail(message: String): Nothing { 
   	throw IllegalStateException (rnessage) 
   }
   ```

   



## 6.3 集合与数组

### 6.3.1 可空性和集合

1. 集合类可以包含null。List<Int?>  可以持有 Int 或者null。

   ```kotlin
   fun readNumbers(reader: BufferedReader): List<Int?> {
       val result = ArrayList<Int?>()
       for (line in reader.lineSequence()) {
           try {
               val number = line.toInt()
               result.add(number)
           }
           catch(e: NumberFormatException) {
               result.add(null)
           }
       }
       return result
   }
   ```

2. 变量自己类型的可空性和用作类型参数的类型的可空性是有区别的。包含可空 Int 的列表和包含 Int 的可空列表之间的区别如图

   ![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\列表可空性.jpg)

3. filterNotNull 标准函数库，遍历一个包含可空值的集合并过滤掉 null。

### 6.3.2 只读集合和可变集合

1. kotlin .collections.Collection 只读集合  和 kotlin.collections MutableCollection 可变集合，继承自Collection。

2. **只读集合不一定是不可变的**，因为它可能只是同一个集合的众多引用中的一个。因此，**解只读集合并不总是线程安全的**，在多线程环境下处理数据需要

   保证代码正确地同步了对数据的访问，或者使用支持并发访问的数据结构。

3. 

   ![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\集合的两个不同引用.jpg)





### 6.3.3 Kotlin集合和Java

1. **Java 并不会区分只读集合与可变集合，即Kotlin 中把集合声明成只读的， Java 代码也能够修改这个集合。**

2. Kotlin中的集合创建函数

   | 集合类型 | 只读   | 可变                                                |
   | -------- | ------ | --------------------------------------------------- |
   | List     | listOf | mutableListOf、 arrayListOf                         |
   | Set      | setOf  | mutableSetOf、hashSetOf、linkedSetOf、sortedSetOf   |
   | Map      | mapOf  | mutableMapOf、 hashMapOf、 linkedMapOf、sortedMapOf |



### 6.3.4 作为平台类型的集合



### 6.3.5 对象和基本数据类型的数组

1. Kotilin 中的数组是一个带有类型参数的类，其元素类型被指定为相应的类型参数。

2. Kotlin中创建数组的方式

   * arrayOf 函数创建一个数组，它包含的元素是指定为该函数的实参

   * arrayOfNulls 创建一个给定大小的数组，包含的是null元素。当然，它只能用来创建包含元素类型可空的数组。

   * Array 构造方法接收数组的大小和一个 ambda 表达式，调用lambda表达式来创建每一个数组元素。这就是使用非空元素类型来初始化数组，但不用显

     式地传递每个元素的方式。

3. 数组类型的类型参数始终会变成Java的对象类型，申明Array<Int>会转换成装箱类型的数组。如果只想使用基础类型，Kotlin提供了基本数据类型的数组，例如IntArray、ByteArray、CharArray、BooleanArray等。创建方式有三种：

   * 该类型的构造方法接收 size 参数并返回 个使用对应基本数据类型默认值（通常是 ）初始化好的数组。

     ```kotlin
     val fiveZeros = IntArray(S)
     ```

   * 工厂函数（ IntArray的intArrayOf ，以及其他数组类型的函数〉接收可变长参数的值并创建存储这些值的数组。

     ```kotlin
     val fiveZerosToo = intArrayOf(0, 0, 0, 0, 0）
     ```

   * 另一种构造方法，接收一个大小和一个用来初始化每个元素的 lambda

     ```kotlin
     val squares = IntArray(5) { i -> (i+1) * (i+1) }
     ```

4. 适用于集合的扩展函数，大部分都适用于数组。







# 7. 运算符重载和其他约定



## 7.1 重载算术运算符

### 7.1.2 重载二元算术运算

1. 使用 operator 关键字用于重载运算符的所有函数。

2. 求和重载

   ```kotlin
   data class Point(val x: Int, val y: Int)
   
   operator fun Point.plus(other: Point): Point {
       return Point(x + other.x, y + other.y)
   }
   
   fun main(args: Array<String>) {
       val p1 = Point(10, 20)
       val p2 = Point(30, 40)
       println(p1 + p2)
   }
   ```

3. 可以重载的二元算术运算符。Kotlin限定了能重载哪些运算符，以及需要在类中定义的对应名字的函数名称。

   ![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\可重载的二元算术运算符.jpg)



### 7.1.2 重载复合赋值运算符

1. 像 +=、-= 等这些运算符被称为复合赋值运算符

### 7.1.3 重载一元函数

1. 重载一元运算符的过程与前面看到的方式相同：用预先定义的一个名称来声明函数（成员函数或扩展函数），并用修饰符 operator 标记。

   ![](C:\Users\shenj\Documents\GitHub\android_guideline\Kotlin知识体系\img\定义一个一元运算符.jpg)



## 7.2 重载比较运算符

### 7.2.1 等号运算符 ："equals"

1. 如果在 Kotlin中使用 == 运算符，它将被转换成 equals 方法的调用。使用 != 运算符也会被转换成 equals 函数的调用。

2. == 和 != 可以用于可空运算数，因为这些运算符事实上会检查运算数是否为 null，比较 == 会检查 a 是否为非空，如果不是，就调用 a.equals (b)；否则，只有两个参数都是空引用，结果才是 true

3. 重载equals

   ```kotlin
   class Point(val x: Int, val y: Int) {
       override fun equals(obj: Any?): Boolean {
           if (obj === this) return true
           if (obj !is Point) return false
           return obj.x == x && obj.y == y
       }
   }
   
   fun main(args: Array<String>) {
       println(Point(10, 20) == Point(10, 20))
       println(Point(10, 20) != Point(5, 5))
       println(null == Point(1, 2))
   }
   ```

   

### 7.2.2 排序运算符：compareTo

1. Kotlin 支持Java中相同的 Comparable 接口，但是接口中定义的 compareTo 方法可以按约定调用，比较运算符（＜，＞，＜＝和＞＝） 的使用将被转换为 compareTo，compare 的返回类型必须为 Int。p1 < p2 表达式等价于 p1 .compareTo(p2)   < 0。其他比较运算符的运算方式也是完全一样的。
2. compareValuesBy 函数来简洁地实现compareTo 方法。这个函数接收用来计算比较值的一系列回调，按顺序依次调用回调方法，两两一组分别做比较，井返回结果。



## 7.3 集合与区间的约定





## 7.5 重用属性访问的逻辑：委托属性







### 使用委托属性：惰性初始化和 by lazy()

lazy 函数返回一个对象，该对象具有一个名为 getValue 且签名正确的方法，因此可以把它与 by 关键字一起使用来创建一个委托属性。 lazy 的参数是一个lambda ，可以调用它来初始化这个值。默认情况下， lazy函数是线程安全的，如果需要，可以设置其他选项来告诉它要使用哪个锁，或者完全避开同步，如果该类永远不会在多线程环境中使用。





# 8. 阶函数： Lambda作为形参和返回僵

## 声明高阶函数

### 函数类型

声明函数类型，需要将函数参数类型放在括号中，紧接着是 个箭头和函数的返回类型





# 9. 泛型



# 协程



# Kotlin开源项目

* [KotlinMvp](https://github.com/git-xuhao/KotlinMvp) ： 基于Kotlin+MVP+Retrofit+RxJava+Glide 等架构实现短视频类小项目，简约风格及详细注释





# 参考（综合）

* [《Kotlin实战》笔记](《Kotlin实战》.xmind)    

* [两万六千字带你Kotlin入门](https://github.com/leavesC/AndroidGuide/blob/master/kotlin_core/%E4%B8%A4%E4%B8%87%E5%85%AD%E5%8D%83%E5%AD%97%E5%B8%A6%E4%BD%A0Kotlin%E5%85%A5%E9%97%A8.md)

* [Kotlin 协程入门这一篇就够了](https://juejin.cn/post/6844903871655968781)

* [一文快速入门 Kotlin 协程](https://juejin.cn/post/6908271959381901325)

  