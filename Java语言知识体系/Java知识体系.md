# Java知识体系

## 1. Java 基础



### 数据类型

- 知识点
  - [基本类型](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#基本类型)

    - byte/8

    - char/16

    - short/16

    - int/32

    - float/32

    - long/64

    - double/64

    - boolean/~

      > boolean 只有两个值：true、false，可以使用 1 bit 来存储，但是具体大小没有明确规定。JVM 会在编译时期将 boolean 类型的数据转换为 int，使用 1 来表示 true，0 表示 false。JVM 支持 boolean 数组，但是是通过读写 byte 数组来实现的。

  - [包装类型](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#包装类型)

  - [**缓存池**](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#缓存池)

    new Integer(123) 与 Integer.valueOf(123) 的区别在于：

    - new Integer(123) 每次都会新建一个对象；
    - Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。

    ```
    Integer x = new Integer(123);
    Integer y = new Integer(123);
    System.out.println(x == y);    // false
    Integer z = Integer.valueOf(123);
    Integer k = Integer.valueOf(123);
    System.out.println(z == k);   // true
    ```

    valueOf() 方法的实现比较简单，就是先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容。

    ```
    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
    ```

    在 Java 8 中，Integer 缓存池的大小默认为 -128~127。

    ```
    static final int low = -128;
    static final int high;
    static final Integer cache[];
    
    static {
        // high value may be configured by property
        int h = 127;
        String integerCacheHighPropValue =
            sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
        if (integerCacheHighPropValue != null) {
            try {
                int i = parseInt(integerCacheHighPropValue);
                i = Math.max(i, 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
            } catch( NumberFormatException nfe) {
                // If the property cannot be parsed into an int, ignore it.
            }
        }
        high = h;
    
        cache = new Integer[(high - low) + 1];
        int j = low;
        for(int k = 0; k < cache.length; k++)
            cache[k] = new Integer(j++);
    
        // range [-128, 127] must be interned (JLS7 5.1.7)
        assert IntegerCache.high >= 127;
    }
    ```

    编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。

    ```
    Integer m = 123;
    Integer n = 123;
    System.out.println(m == n); // true
    ```

    基本类型对应的缓冲池如下：

    - boolean values true and false
    - all byte values
    - short values between -128 and 127
    - int values between -128 and 127
    - char in the range \u0000 to \u007F

    在使用这些基本类型对应的包装类型时，如果该数值范围在缓冲池范围内，就可以直接使用缓冲池中的对象。

    在 jdk 1.8 所有的数值类缓冲池中，Integer 的缓冲池 IntegerCache 很特殊，这个缓冲池的下界是 - 128，上界默认是 127，但是这个上界是可调的，在启动 jvm 的时候，通过 -XX:AutoBoxCacheMax=<size> 来指定这个缓冲池的大小，该选项在 JVM 初始化的时候会设定一个名为 java.lang.IntegerCache.high 系统属性，然后 IntegerCache 初始化的时候就会读取该系统属性来决定上界。

    [StackOverflow : Differences between new Integer(123), Integer.valueOf(123) and just 123](https://stackoverflow.com/questions/9030817/differences-between-new-integer123-integer-valueof123-and-just-123)



### String

- [概览](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#概览)

  String 被声明为 final，因此它不可被继承。(Integer 等包装类也不能被继承）

  在 Java 8 中，String 内部使用 char 数组存储数据。

  ```
  public final class String
      implements java.io.Serializable, Comparable<String>, CharSequence {
      /** The value is used for character storage. */
      private final char value[];
  }
  ```

  在 Java 9 之后，String 类的实现改用 byte 数组存储字符串，同时使用 `coder` 来标识使用了哪种编码。

  ```
  public final class String
      implements java.io.Serializable, Comparable<String>, CharSequence {
      /** The value is used for character storage. */
      private final byte[] value;
  
      /** The identifier of the encoding used to encode the bytes in {@code value}. */
      private final byte coder;
  }
  ```

  value 数组被声明为 final，这意味着 value 数组初始化之后就不能再引用其它数组。并且 String 内部没有改变 value 数组的方法，因此可以保证 String 不可变。

- [不可变的好处](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#不可变的好处)

  **1. 可以缓存 hash 值**

  因为 String 的 hash 值经常被使用，例如 String 用做 HashMap 的 key。不可变的特性可以使得 hash 值也不可变，因此只需要进行一次计算。

  **2. String Pool 的需要**

  如果一个 String 对象已经被创建过了，那么就会从 String Pool 中取得引用。只有 String 是不可变的，才可能使用 String Pool。

  ![img](https://camo.githubusercontent.com/a8170bdc42d86acbe28eab2dabdc32a94ecf5441fd4f5b1a539b8848df7d8c7b/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f696d6167652d32303139313231303030343133323839342e706e67)

  

  **3. 安全性**

  String 经常作为参数，String 不可变性可以保证参数不可变。例如在作为网络连接参数的情况下如果 String 是可变的，那么在网络连接过程中，String 被改变，改变 String 的那一方以为现在连接的是其它主机，而实际情况却不一定是。

  **4. 线程安全**

  String 不可变性天生具备线程安全，可以在多个线程中安全地使用。

  [Program Creek : Why String is immutable in Java?](https://www.programcreek.com/2013/04/why-string-is-immutable-in-java/)

- [String, StringBuffer and StringBuilder](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#string-stringbuffer-and-stringbuilder)

  **1. 可变性**

  - String 不可变
  - StringBuffer 和 StringBuilder 可变

  **2. 线程安全**

  - String 不可变，因此是线程安全的
  - StringBuilder 不是线程安全的
  - StringBuffer 是线程安全的，内部使用 synchronized 进行同步

- [String Pool](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#string-pool)

  字符串常量池（String Pool）保存着所有字符串字面量（literal strings），这些字面量在编译时期就确定。不仅如此，还可以使用 String 的 intern() 方法在运行过程将字符串添加到 String Pool 中。

  当一个字符串调用 intern() 方法时，如果 String Pool 中已经存在一个字符串和该字符串值相等（使用 equals() 方法进行确定），那么就会返回 String Pool 中字符串的引用；否则，就会在 String Pool 中添加一个新的字符串，并返回这个新字符串的引用。

  下面示例中，s1 和 s2 采用 new String() 的方式新建了两个不同字符串，而 s3 和 s4 是通过 s1.intern() 和 s2.intern() 方法取得同一个字符串引用。intern() 首先把 "aaa" 放到 String Pool 中，然后返回这个字符串引用，因此 s3 和 s4 引用的是同一个字符串。

  ```
  String s1 = new String("aaa");
  String s2 = new String("aaa");
  System.out.println(s1 == s2);           // false
  String s3 = s1.intern();
  String s4 = s2.intern();
  System.out.println(s3 == s4);           // true
  ```

  如果是采用 "bbb" 这种字面量的形式创建字符串，会自动地将字符串放入 String Pool 中。

  ```
  String s5 = "bbb";
  String s6 = "bbb";
  System.out.println(s5 == s6);  // true
  ```

  在 Java 7 之前，String Pool 被放在运行时常量池中，它属于永久代。而在 Java 7，String Pool 被移到堆中。这是因为永久代的空间有限，在大量使用字符串的场景下会导致 OutOfMemoryError 错误。

  - [StackOverflow : What is String interning?](https://stackoverflow.com/questions/10578984/what-is-string-interning)
  - [深入解析 String#intern](https://tech.meituan.com/in_depth_understanding_string_intern.html)

- [new String("abc")](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#new-stringabc)

  使用这种方式一共会创建两个字符串对象（前提是 String Pool 中还没有 "abc" 字符串对象）。

  - "abc" 属于字符串字面量，因此编译时期会在 String Pool 中创建一个字符串对象，指向这个 "abc" 字符串字面量；
  - 而使用 new 的方式会在堆中创建一个字符串对象。

  创建一个测试类，其 main 方法中使用这种方式来创建字符串对象。

  ```
  public class NewStringTest {
      public static void main(String[] args) {
          String s = new String("abc");
      }
  }
  ```

  使用 javap -verbose 进行反编译，得到以下内容：

  ```
  // ...
  Constant pool:
  // ...
     #2 = Class              #18            // java/lang/String
     #3 = String             #19            // abc
  // ...
    #18 = Utf8               java/lang/String
    #19 = Utf8               abc
  // ...
  
    public static void main(java.lang.String[]);
      descriptor: ([Ljava/lang/String;)V
      flags: ACC_PUBLIC, ACC_STATIC
      Code:
        stack=3, locals=2, args_size=1
           0: new           #2                  // class java/lang/String
           3: dup
           4: ldc           #3                  // String abc
           6: invokespecial #4                  // Method java/lang/String."<init>":(Ljava/lang/String;)V
           9: astore_1
  // ...
  ```

  在 Constant Pool 中，#19 存储这字符串字面量 "abc"，#3 是 String Pool 的字符串对象，它指向 #19 这个字符串字面量。在 main 方法中，0: 行使用 new #2 在堆中创建一个字符串对象，并且使用 ldc #3 将 String Pool 中的字符串对象作为 String 构造函数的参数。

  以下是 String 构造函数的源码，可以看到，在将一个字符串对象作为另一个字符串对象的构造函数参数时，并不会完全复制 value 数组内容，而是都会指向同一个 value 数组。

  ```
  public String(String original) {
      this.value = original.value;
      this.hash = original.hash;
  }
  ```



### 运算

- [参数传递](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#参数传递)
  - 值传递和引用传递
- [float 与 double](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#float-与-double)
  - Java 不能隐式执行向下转型，因为这会使得精度降低。
- [隐式类型转换](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#隐式类型转换)
- [switch](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#switch)
  - switch支持int、String， 不支持 long、float、double



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

  |               | 同一个类 | 同一个包 | 不同包的子类 | 不同包的非子类 |
  | ------------- | -------- | -------- | ------------ | -------------- |
  | public        | √        | √        | √            | √              |
  | protected     | √        | √        | √            |                |
  | 默认(default) | √        | √        |              |                |
  | private       | √        |          |              |                |

- [抽象类与接口](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#抽象类与接口)

- [super](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#super)

- [重写与重载](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#重写与重载)



### 反射

* 知识点
  * 什么是Class

    对于每一种类，Java 虚拟机都会初始化出一个 Class 类型的实例，每当我们编写并且编译一个新创建的类就会产生一个对应 Class 对象，并且这个 Class 对象会被保存在同名 .class 文件里。当我们 new 一个新对象或者引用静态成员变量时，Java 虚拟机(JVM)中的类加载器系统会将对应 Class 对象加载到 JVM 中，然后 JVM 再根据这个类型信息相关的Class 对象创建我们需要实例对象或者提供静态变量的引用值。

  * 如何获取Class

    * Object.getClass()  ，通过对象实例获取对应 Class 对象

      ```java
      //Returns the Class for String
      Class c = "foo".getClass();
      ```

      

    * The .class Syntax  ， 通过类的类型获取Class对象,基本类型同样可以使用这种方法

      ```java
      //The `.class` syntax returns the Class corresponding to the type `boolean`.
      Class c = boolean.class;  
      
      //Returns the Class for String
      Class c = String.class;
      ```

    * Class.forName()  ，通过类的全限定名获取Class对象， 基本类型无法使用此方法

      ```java
      Class c = Class.forName("java.lang.String");
      ```

      

    * Class.getSuperclass()获得给定类的父类 Class

      ```java
      // javax.swing.JButton的父类是javax.swing.AbstractButton
      Class c = javax.swing.JButton.class.getSuperclass();
      ```

      

  * 通过Class获取类修饰符和类型

    ![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt5tL9GD0n77s3FwJiarzao9SKeiccYrAOy1qStMPfiadTQhuy4bmt3kx18tyf5zaq3ITOmRK3ib4Be6eA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

    

  * Member

    * Field

      通过 Field 你可以访问给定对象的类变量，包括获取变量的类型、修饰符、注解、变量名、变量的值或者重新设置变量值，即使变量是 private 的。

      1. Class 提供了4种方法获得给定类的 Field

      - - getDeclaredField(String name)   

          获取指定的变量（只要是声明的变量都能获得，包括 private）

        - getField(String name)    

          获取指定的变量（只能获得 public 的）

        - getDeclaredFields()        

          获取所有声明的变量（包括 private）

        - getFields()

          获取所有的 public 变量

      2. 通过Field获取变量类型、修饰符、注解

         ```java
         public void testField(){
                 Class c = Cat.class;
                 Field[] fields = c.getDeclaredFields();
                 for(Field f : fields){
                     StringBuilder builder = new StringBuilder();
                     //获取名称
                     builder.append("filed name = ");
                     builder.append(f.getName());
                     //获取类型
                     builder.append(" type = ");
                     builder.append(f.getType());
                     //获取修饰符
                     builder.append(" modifiers = ");
                     builder.append(Modifier.toString(f.getModifiers()));
                     //获取注解
                     Annotation[] ann = f.getAnnotations();
                     if (ann.length != 0) {
                         builder.append(" annotations = ");
                         for (Annotation a : ann){
                             builder.append(a.toString());
                             builder.append(" ");
                         }
                     } else {
                         builder.append("  -- No Annotations --");
                     }
                     Log.d(TAG, builder.toString());
                 }
             }
         ```

      3. 通过Field获取、设置变量值

         ```java
         public void testField(){
                 Cat cat = new Cat("Tom", 2);
                 Class c = cat.getClass();
                 try {
                     //注意获取private变量时，需要用getDeclaredField
                     Field fieldName = c.getDeclaredField("name");
                     Field fieldAge = c.getField("age");
                     // 取消 Java 语言访问权限检查
                     fieldName.setAccessible(true);
                     //反射获取名字, 年龄
                     String name = (String) fieldName.get(cat);
                     int age = fieldAge.getInt(cat);
                     Log.d(TAG, "before set, Cat name = " + name + " age = " + age);
                     //反射重新set名字和年龄
                     fieldName.set(cat, "Timmy");
                     fieldAge.setInt(cat, 3);
                     Log.d(TAG, "after set, Cat " + cat.toString());
                 } catch (NoSuchFieldException e) {
                     e.printStackTrace();
                 } catch (IllegalAccessException e) {
                     e.printStackTrace();
                 }
             }
         ```

         

    * Method

      1. 获取Method

         Class 依然提供了4种方法获取 Method:

         - - getDeclaredMethod(String name, Class<?>... parameterTypes)

             根据方法名获得指定的方法， 参数 name 为方法名，参数 parameterTypes 为方法的参数类型，如 getDeclaredMethod(“eat”, String.class)

           - getMethod(String name, Class<?>... parameterTypes)

             根据方法名获取指定的 public 方法，其它同上

           - getDeclaredMethods()

             获取所有声明的方法

           - getMethods()

             获取所有的 public 方法

         > 注意：获取带参数方法时，如果参数类型错误会报 NoSuchMethodException，对于参数是泛型的情况，泛型须当成Object处理（Object.class）

      2. 通过Method获取方法返回类型

         - getReturnType()  获取目标方法返回类型对应的 Class 对象
         - getGenericReturnType()  获取目标方法返回类型对应的 Type 对象

         > 这两个方法有啥区别呢？
         >
         > - getReturnType()返回类型为 Class，getGenericReturnType() 返回类型为 Type; Class 实现 Type。
         >
         > - 返回值为普通简单类型如 Object, int, String 等，getGenericReturnType() 返回值和 getReturnType() 一样
         >
         >   例如 public String function1()，那么各自返回值为：
         >
         > - - getReturnType() : class java.lang.String
         >   - getGenericReturnType() : class java.lang.String
         >
         > - 返回值为泛型
         >
         >   例如 public T function2()，那么各自返回值为：
         >
         > - - getReturnType() : class java.lang.Object
         >   - getGenericReturnType() : T
         >
         > - 返回值为参数化类型
         >
         >   例如public Class<String> function3()，那么各自返回值为：
         >
         > - - getReturnType() : class java.lang.Class
         >   - getGenericReturnType() : java.lang.Class<java.lang.String>
         >
         > 其实反射中所有形如 getGenericXXX()的方法规则都与上面所述类似。

         

      3. 通过Method获取方法参数类型

         * getParameterTypes() 获取目标方法各参数类型对应的 Class 对象

         * getGenericParameterTypes() 获取目标方法各参数类型对应的 Type 对象

           返回值为数组，它俩区别同上 “方法返回类型的区别” 。

         

      4. 通过Method获取方法声明抛出的异常的类型

         * getExceptionTypes() 获取目标方法抛出的异常类型对应的 Class 对象

         * getGenericExceptionTypes()  获取目标方法抛出的异常类型对应的 Type 对象
           返回值为数组，区别同上

           

      5. 通过Method获取方法参数名称

         .class 文件中默认不存储方法参数名称，如果想要获取方法参数名称，需要在编译的时候加上 -parameters 参数。(构造方法的参数获取方法同样)

         ```java
         //这里的m可以是普通方法Method，也可以是构造方法Constructor
         //获取方法所有参数
         Parameter[] params = m.getParameters();
         for (int i = 0; i < params.length; i++) {
             Parameter p = params[i];
             p.getType();   //获取参数类型
             p.getName();  //获取参数名称，如果编译时未加上`-parameters`，返回的名称形如`argX`, X为参数在方法声明中的位置，从0开始
             p.getModifiers(); //获取参数修饰符
             p.isNamePresent();  //.class文件中是否保存参数名称, 编译时加上`-parameters`返回true,反之flase
         }
         ```

         

      6. 通过Method获取方法修饰符

         ```java
         method.getModifiers();
         ```

         

      7. 通过反射调用方法

         反射通过Method的invoke()方法来调用目标方法。第一个参数为需要调用的目标类对象，如果方法为static的，则该参数为null。后面的参数都为目标方法的参数值，顺序与目标方法声明中的参数顺序一致。

         ```java
         public native Object invoke(Object obj, Object... args)
                     throws IllegalAccessException, IllegalArgumentException, InvocationTargetException
         ```

         > 注意：如果方法是private的，可以使用 method.setAccessible(true) 方法绕过权限检查

         ```java
         Class<?> c = Cat.class;
          try {
              //构造Cat实例
              Constructor constructor = c.getConstructor(String.class, int.class);
              Object cat = constructor.newInstance( "Jack", 3);
              //调用无参方法
              Method sleep = c.getDeclaredMethod("sleep");
              sleep.invoke(cat);
              //调用定项参数方法
              Method eat = c.getDeclaredMethod("eat", String.class);
              eat.invoke(cat, "grass");
              //调用不定项参数方法
              //不定项参数可以当成数组来处理
              Class[] argTypes = new Class[] { String[].class };
              Method varargsEat = c.getDeclaredMethod("eat", argTypes);
              String[] foods = new String[]{
                   "grass", "meat"
              };
              varargsEat.invoke(cat, (Object)foods);
           } catch (InstantiationException | IllegalAccessException | NoSuchMethodException | InvocationTargetException e) {
              e.printStackTrace();
          }
         ```

         被调用的方法本身所抛出的异常在反射中都会以 InvocationTargetException 抛出。换句话说，反射调用过程中如果异常 InvocationTargetException 抛出，说明反射调用本身是成功的，因为这个异常是目标方法本身所抛出的异常。

         

    * Constructor

      1. 获取构造方法

         和 Method 一样，Class 也为 Constructor 提供了4种方法获取

         - - getDeclaredConstructor(Class<?>... parameterTypes)

             获取指定构造函数，参数 parameterTypes 为构造方法的参数类型

           - getConstructor(Class<?>... parameterTypes)

             获取指定 public 构造函数，参数 parameterTypes 为构造方法的参数类型

           - getDeclaredConstructors()

             获取所有声明的构造方法

           - getConstructors()

             获取所有的 public 构造方法

         构造方法的名称、限定符、参数、声明的异常等获取方法都与 Method 类似，请参照Method。

      2. 创建对象

         通过反射有两种方法可以创建对象：

         - java.lang.reflect.Constructor.newInstance()
         - Class.newInstance()

         > **一般来讲，我们优先使用第一种方法**；那么这两种方法有何异同呢？
         >
         > 1. Class.newInstance()仅可用来调用无参的构造方法；Constructor.newInstance()可以调用任意参数的构造方法。
         > 2. Class.newInstance()会将构造方法中抛出的异常不作处理原样抛出; Constructor.newInstance()会将构造方法中抛出的异常都包装成 InvocationTargetException 抛出。
         > 3. Class.newInstance()需要拥有构造方法的访问权限; Constructor.newInstance()可以通过 setAccessible(true) 方法绕过访问权限访问 private 构造方法。

         ```java
         Class<?> c = Cat.class;
         try {
             Constructor constructor = c.getConstructor(String.class, int.class);
             Cat cat = (Cat) constructor.newInstance( "Jack", 3);
         } catch (InstantiationException | IllegalAccessException | NoSuchMethodException | InvocationTargetException e) {
             e.printStackTrace();
         }
         ```

         

  * 副作用
    
    * 性能开销
    * 安全限制
    * 内部曝光
* 参考资料
  
  * [一篇文章带你学懂Java反射](https://mp.weixin.qq.com/s/PYjFA1v_mwMpyKACI3B9PQ)



### [异常](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#八异常)

Throwable 可以用来表示任何可以作为异常抛出的类，分为两种： **Error** 和 **Exception**。其中 Error 用来表示 JVM 无法处理的错误，Exception 分为两种：

- **受检异常** ：需要用 try...catch... 语句捕获并进行处理，并且可以从异常中恢复；
- **非受检异常** ：是程序运行时错误，例如除 0 会引发 Arithmetic Exception，此时程序崩溃并且无法恢复。

### [泛型](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#九泛型)



### [注解](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#十注解)



## Java容器

### 综合知识

* 参考资料
  * [Java 容器.md](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E5%AE%B9%E5%99%A8.md#java-%E5%AE%B9%E5%99%A8)
  * [面试官: 我必问的容器知识点!](https://juejin.cn/post/6844904115001098254)
  * [Java集合类](https://blog.csdn.net/Siobhan/article/details/51455143)



### [Collection](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 容器.md#collection)

<img src="https://camo.githubusercontent.com/c0506ba8f5134d89ed6a398e1c165865d50c68f6c7af4e01b75248a95e0da37d/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f696d6167652d32303139313230383232303934383038342e706e67" style="zoom: 80%;" />



#### 1. Set

- TreeSet：基于红黑树实现，支持有序性操作，例如根据一个范围查找元素的操作。但是查找效率不如 HashSet，HashSet 查找的时间复杂度为 O(1)，TreeSet 则为 O(logN)。
- HashSet：基于哈希表实现，支持快速查找，但不支持有序性操作。并且失去了元素的插入顺序信息，也就是说使用 Iterator 遍历 HashSet 得到的结果是不确定的。
- LinkedHashSet：具有 HashSet 的查找效率，并且内部使用双向链表维护元素的插入顺序。



#### 2. List

* **ArrayList：基于动态数组实现，支持随机访问。**
  
  1. 概览
  
     因为 ArrayList 是基于数组实现的，所以支持快速随机访问。RandomAccess 接口标识着该类支持快速随机访问。
  
   ```
     public class ArrayList<E> extends AbstractList<E>
           implements List<E>, RandomAccess, Cloneable, java.io.Serializable
   ```
  
     数组的默认大小为 10。
  
     ```
     private static final int DEFAULT_CAPACITY = 10;
     ```
  
     ![img](https://camo.githubusercontent.com/dfd2ffd9b9f090b35d2a2ae1af4e3a700dfc234b2f892d310806534001cd0885/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f696d6167652d32303139313230383233323232313236352e706e67)
  
     
  
  2. 扩容
  
     添加元素时使用 ensureCapacityInternal() 方法来保证容量足够，如果不够时，需要使用 grow() 方法进行扩容，新容量的大小为 `oldCapacity + (oldCapacity >> 1)`，即 oldCapacity+oldCapacity/2。其中 oldCapacity >> 1 需要取整，所以新容量大约是旧容量的 1.5 倍左右。（oldCapacity 为偶数就是 1.5 倍，为奇数就是 1.5 倍-0.5）
  
     扩容操作需要调用 `Arrays.copyOf()` 把原数组整个复制到新数组中，这个操作代价很高，因此最好在创建 ArrayList 对象时就指定大概的容量大小，减少扩容操作的次数。
  
     ```java
     public boolean add(E e) {
         ensureCapacityInternal(size + 1);  // Increments modCount!!
         elementData[size++] = e;
         return true;
     }
     
     private void ensureCapacityInternal(int minCapacity) {
         if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
             minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
         }
         ensureExplicitCapacity(minCapacity);
     }
     
     private void ensureExplicitCapacity(int minCapacity) {
         modCount++;
         // overflow-conscious code
         if (minCapacity - elementData.length > 0)
             grow(minCapacity);
     }
     
     private void grow(int minCapacity) {
         // overflow-conscious code
         int oldCapacity = elementData.length;
         int newCapacity = oldCapacity + (oldCapacity >> 1);
         if (newCapacity - minCapacity < 0)
             newCapacity = minCapacity;
         if (newCapacity - MAX_ARRAY_SIZE > 0)
             newCapacity = hugeCapacity(minCapacity);
         // minCapacity is usually close to size, so this is a win:
         elementData = Arrays.copyOf(elementData, newCapacity);
     }
     ```
  
     
  
  3. 删除元素
  
     需要调用 System.arraycopy() 将 index+1 后面的元素都复制到 index 位置上，该操作的时间复杂度为 O(N)，可以看到 ArrayList 删除元素的代价是非常高的。
  
     ```java
     public E remove(int index) {
         rangeCheck(index);
         modCount++;
         E oldValue = elementData(index);
         int numMoved = size - index - 1;
         if (numMoved > 0)
             System.arraycopy(elementData, index+1, elementData, index, numMoved);
         elementData[--size] = null; // clear to let GC do its work
         return oldValue;
     }
     ```
  
     
  
  4. 序列化
  
     ArrayList 基于数组实现，并且具有动态扩容特性，因此保存元素的数组不一定都会被使用，那么就没必要全部进行序列化。
  
     保存元素的数组 elementData 使用 transient 修饰，该关键字声明数组默认不会被序列化。
  
     ```java
     transient Object[] elementData; // non-private to simplify nested class access
     ```
  
     
  
     ArrayList 实现了 writeObject() 和 readObject() 来控制只序列化数组中有元素填充那部分内容。
  
     ```java
     private void readObject(java.io.ObjectInputStream s)
         throws java.io.IOException, ClassNotFoundException {
         elementData = EMPTY_ELEMENTDATA;
     
         // Read in size, and any hidden stuff
         s.defaultReadObject();
     
         // Read in capacity
         s.readInt(); // ignored
     
         if (size > 0) {
             // be like clone(), allocate array based upon size not capacity
             ensureCapacityInternal(size);
     
             Object[] a = elementData;
             // Read in all elements in the proper order.
             for (int i=0; i<size; i++) {
                 a[i] = s.readObject();
             }
         }
     }
     private void writeObject(java.io.ObjectOutputStream s)
         throws java.io.IOException{
         // Write out element count, and any hidden stuff
         int expectedModCount = modCount;
         s.defaultWriteObject();
     
         // Write out size as capacity for behavioural compatibility with clone()
         s.writeInt(size);
     
         // Write out all elements in the proper order.
         for (int i=0; i<size; i++) {
             s.writeObject(elementData[i]);
         }
     
         if (modCount != expectedModCount) {
             throw new ConcurrentModificationException();
         }
     }
     ```
  
     
  
     序列化时需要使用 ObjectOutputStream 的 writeObject() 将对象转换为字节流并输出。而 writeObject() 方法在传入的对象存在 writeObject() 的时候会去反射调用该对象的 writeObject() 来实现序列化。反序列化使用的是 ObjectInputStream 的 readObject() 方法，原理类似。
  
     ```java
     ArrayList list = new ArrayList();
     ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(file));
     oos.writeObject(list);
     ```
  
     
  
  5. Fail-Fast
  
     modCount 用来记录 ArrayList 结构发生变化的次数。结构发生变化是指添加或者删除至少一个元素的所有操作，或者是调整内部数组的大小，仅仅只是设置元素的值不算结构发生变化。
  
     在进行序列化或者迭代等操作时，需要比较操作前后 modCount 是否改变，如果改变了需要抛出 ConcurrentModificationException。代码参考上节序列化中的 writeObject() 方法。



* **Vector**：和 ArrayList 类似，但它是线程安全的。

    1. 同步

       它的实现与 ArrayList 类似，但是使用了 synchronized 进行同步。

       ```java
       public synchronized boolean add(E e) {
           modCount++;
           ensureCapacityHelper(elementCount + 1);
           elementData[elementCount++] = e;
           return true;
       }
       
       public synchronized E get(int index) {
           if (index >= elementCount)
               throw new ArrayIndexOutOfBoundsException(index);
       
           return elementData(index);
       }
       ```

       

    2. 扩容

       Vector 的构造函数可以传入 capacityIncrement 参数，它的作用是在扩容时使容量 capacity 增长 capacityIncrement。如果这个参数的值小于等于 0，扩容时每次都令 capacity 为原来的两倍。

       ```java
       public Vector(int initialCapacity, int capacityIncrement) {
           super();
           if (initialCapacity < 0)
               throw new IllegalArgumentException("Illegal Capacity: "+
                                                  initialCapacity);
           this.elementData = new Object[initialCapacity];
           this.capacityIncrement = capacityIncrement;
       }
       private void grow(int minCapacity) {
           // overflow-conscious code
           int oldCapacity = elementData.length;
           int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                            capacityIncrement : oldCapacity);
           if (newCapacity - minCapacity < 0)
               newCapacity = minCapacity;
           if (newCapacity - MAX_ARRAY_SIZE > 0)
               newCapacity = hugeCapacity(minCapacity);
           elementData = Arrays.copyOf(elementData, newCapacity);
       }
       ```

       

       调用没有 capacityIncrement 的构造函数时，capacityIncrement 值被设置为 0，也就是说默认情况下 Vector 每次扩容时容量都会翻倍。

       ```java
       public Vector(int initialCapacity) {
           this(initialCapacity, 0);
       }
       
       public Vector() {
           this(10);
       }
       ```

       

    3. 与 ArrayList 的比较
       * Vector 是同步的，因此开销就比 ArrayList 要大，访问速度更慢。最好使用 ArrayList 而不是 Vector，因为同步操作完全可以由程序员自己来控制；
       * Vector 每次扩容请求其大小的 2 倍（也可以通过构造函数设置增长的容量），而 ArrayList 是 1.5 倍。

    4. 替代方案

       可以使用 `Collections.synchronizedList();` 得到一个线程安全的 ArrayList。

       ```java
       List<String> list = new ArrayList<>();
       List<String> synList = Collections.synchronizedList(list);
       ```

       也可以使用 concurrent 并发包下的 CopyOnWriteArrayList 类。

       ```java
       List<String> list = new CopyOnWriteArrayList<>();
       ```

       

    

* **CopyOnWriteArrayList** 

    1. 读写分离

       写操作在一个复制的数组上进行，读操作还是在原始数组中进行，读写分离，互不影响。

       写操作需要加锁，防止并发写入时导致写入数据丢失。

       写操作结束之后需要把原始数组指向新的复制数组。

       ```java
       public boolean add(E e) {
           final ReentrantLock lock = this.lock;
           lock.lock();
           try {
               Object[] elements = getArray();
               int len = elements.length;
               Object[] newElements = Arrays.copyOf(elements, len + 1);
               newElements[len] = e;
               setArray(newElements);
               return true;
           } finally {
               lock.unlock();
           }
       }
       
       final void setArray(Object[] a) {
           array = a;
       }
       @SuppressWarnings("unchecked")
       private E get(Object[] a, int index) {
           return (E) a[index];
       }
       ```

       

    2. 适用场景

       CopyOnWriteArrayList 在写操作的同时允许读操作，大大提高了读操作的性能，因此很适合读多写少的应用场景。

       但是 CopyOnWriteArrayList 有其缺陷：

       * 内存占用：在写操作时需要复制一个新的数组，使得内存占用为原来的两倍左右；
       * 数据不一致：读操作不能读取实时性的数据，因为部分写操作的数据还未同步到读数组中。

       所以 CopyOnWriteArrayList 不适合内存敏感以及对实时性要求很高的场景。



* **LinkedList**：基于双向链表实现，只能顺序访问，但是可以快速地在链表中间插入和删除元素。不仅如此，LinkedList 还可以用作栈、队列和双向队列。

    1. 概览

       基于双向链表实现，使用 Node 存储链表节点信息。

       ```java
       private static class Node<E> {
           E item;
           Node<E> next;
           Node<E> prev;
       }
       ```

       

       每个链表存储了 first 和 last 指针：

       ```java
       transient Node<E> first;
       transient Node<E> last;
       ```

       ![img](https://camo.githubusercontent.com/7462706fa8209587f8090cd86c510afc66f7022e01d12305cb88d1d5c1b019c9/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f696d6167652d32303139313230383233333934303036362e706e67)

    

    2. 与 ArrayList 的比较

       ArrayList 基于动态数组实现，LinkedList 基于双向链表实现。ArrayList 和 LinkedList 的区别可以归结为数组和链表的区别：

       * 数组支持随机访问，但插入删除的代价很高，需要移动大量元素；
       * 链表不支持随机访问，但插入删除只需要改变指针。

    3. LinkedList 实现 栈、列表 

       >  参考 [LinkedList实现栈、队列或者双端队列分析](https://blog.csdn.net/huangfan322/article/details/52756441)   |   [Java双向队列Deque栈与队列](https://blog.csdn.net/u013967628/article/details/85210036)



#### 3. Queue

* LinkedList：可以用它来实现双向队列。

* PriorityQueue：基于堆结构实现，可以用它来实现优先队列。

  > [JAVA中PRIORITYQUEUE详解](https://www.cnblogs.com/Elliott-Su-Faith-change-our-life/p/7472265.html)

* LinkedBlockingQueue 

* ArrayBlockingQueue 

  



#### 4. Deque（双向队列，继承Queue）

 [Java双向队列Deque栈与队列](https://blog.csdn.net/u013967628/article/details/85210036)



### Map

<img src="https://camo.githubusercontent.com/ce6470fc8cfd0f0c74ba53bd16ee9467b21c5ca7fc0566413df4701342a96a15/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f696d6167652d32303230313130313233343333353833372e706e67" style="zoom: 50%;" />

* **TreeMap**：基于红黑树实现。

  

* **HashMap**：基于哈希表实现。

  为了便于理解，以下源码分析以 JDK 1.7 为主。

  1. 存储结构

     内部包含了一个 Entry 类型的数组 table。Entry 存储着键值对。它包含了四个字段，从 next 字段我们可以看出 Entry 是一个链表。即数组中的每个位置被当成一个桶，一个桶存放一个链表。HashMap 使用拉链法来解决冲突，同一个链表中存放哈希值和散列桶取模运算结果相同的 Entry。

     <img src="https://camo.githubusercontent.com/363d11bc36ca5b717ea62374b76ea2a47c287c15b072fd076ab75388abb202a5/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f696d6167652d32303139313230383233343934383230352e706e67" alt="img"  />

     ```java
     transient Entry[] table;
     static class Entry<K,V> implements Map.Entry<K,V> {
         final K key;
         V value;
         Entry<K,V> next;
         int hash;
     
         Entry(int h, K k, V v, Entry<K,V> n) {
             value = v;
             next = n;
             key = k;
             hash = h;
         }
     
         public final K getKey() {
             return key;
         }
     
         public final V getValue() {
             return value;
         }
     
         public final V setValue(V newValue) {
             V oldValue = value;
             value = newValue;
             return oldValue;
         }
     
         public final boolean equals(Object o) {
             if (!(o instanceof Map.Entry))
                 return false;
             Map.Entry e = (Map.Entry)o;
             Object k1 = getKey();
             Object k2 = e.getKey();
             if (k1 == k2 || (k1 != null && k1.equals(k2))) {
                 Object v1 = getValue();
                 Object v2 = e.getValue();
                 if (v1 == v2 || (v1 != null && v1.equals(v2)))
                     return true;
             }
             return false;
         }
     
         public final int hashCode() {
             return Objects.hashCode(getKey()) ^ Objects.hashCode(getValue());
         }
     
         public final String toString() {
             return getKey() + "=" + getValue();
         }
     }
     ```

     

  2. 拉链法的工作原理

     ```java
     HashMap<String, String> map = new HashMap<>();
     map.put("K1", "V1");
     map.put("K2", "V2");
     map.put("K3", "V3");
     ```

     * 新建一个 HashMap，默认大小为 16；

     * 插入 <K1,V1> 键值对，先计算 K1 的 hashCode 为 115，使用除留余数法得到所在的桶下标 115%16=3。

     * 插入 <K2,V2> 键值对，先计算 K2 的 hashCode 为 118，使用除留余数法得到所在的桶下标 118%16=6。

     * 插入 <K3,V3> 键值对，先计算 K3 的 hashCode 为 118，使用除留余数法得到所在的桶下标 118%16=6，插在 <K2,V2> 前面。
     
     应该注意到链表的插入是以头插法方式进行的，例如上面的 <K3,V3> 不是插在 <K2,V2> 后面，而是插入在链表头部。
     
     查找需要分成两步进行：
     
     * 计算键值对所在的桶；
     * 在链表上顺序查找，时间复杂度显然和链表的长度成正比。

  3. put 操作

     ```java
       public V put(K key, V value) {
           if (table == EMPTY_TABLE) {
               inflateTable(threshold);
           }
           // 键为 null 单独处理
           if (key == null)
               return putForNullKey(value);
           int hash = hash(key);
           // 确定桶下标
           int i = indexFor(hash, table.length);
           // 先找出是否已经存在键为 key 的键值对，如果存在的话就更新这个键值对的值为 value
           for (Entry<K,V> e = table[i]; e != null; e = e.next) {
               Object k;
               if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                   V oldValue = e.value;
                   e.value = value;
                   e.recordAccess(this);
                   return oldValue;
               }
           }
       
           modCount++;
           // 插入新键值对
           addEntry(hash, key, value, i);
           return null;
       }
     ```

     HashMap 允许插入键为 null 的键值对。但是因为无法调用 null 的 hashCode() 方法，也就无法确定该键值对的桶下标，只能通过强制指定一个桶下标来存放。HashMap 使用第 0 个桶存放键为 null 的键值对。
     
     ```java
       private V putForNullKey(V value) {
           for (Entry<K,V> e = table[0]; e != null; e = e.next) {
               if (e.key == null) {
                   V oldValue = e.value;
                   e.value = value;
                   e.recordAccess(this);
                   return oldValue;
               }
           }
           modCount++;
           addEntry(0, null, value, 0);
           return null;
       }
     ```
     
     使用链表的头插法，也就是新的键值对插在链表的头部，而不是链表的尾部。
     
     ```java
       void addEntry(int hash, K key, V value, int bucketIndex) {
           if ((size >= threshold) && (null != table[bucketIndex])) {
               resize(2 * table.length);
               hash = (null != key) ? hash(key) : 0;
               bucketIndex = indexFor(hash, table.length);
           }
       
           createEntry(hash, key, value, bucketIndex);
       }
       
       void createEntry(int hash, K key, V value, int bucketIndex) {
           Entry<K,V> e = table[bucketIndex];
           // 头插法，链表头部指向新的键值对
           table[bucketIndex] = new Entry<>(hash, key, value, e);
           size++;
       }
       Entry(int h, K k, V v, Entry<K,V> n) {
           value = v;
           next = n;
           key = k;
           hash = h;
       }
     ```
     
     

  4. 确定桶下标

     很多操作都需要先确定一个键值对所在的桶下标。

     ```java
       int hash = hash(key);
       int i = indexFor(hash, table.length);
     ```

      4.1 计算 hash 值

     ```java
       final int hash(Object k) {
           int h = hashSeed;
           if (0 != h && k instanceof String) {
               return sun.misc.Hashing.stringHash32((String) k);
           }
       
           h ^= k.hashCode();
       
           // This function ensures that hashCodes that differ only by
           // constant multiples at each bit position have a bounded
           // number of collisions (approximately 8 at default load factor).
           h ^= (h >>> 20) ^ (h >>> 12);
           return h ^ (h >>> 7) ^ (h >>> 4);
       }
       public final int hashCode() {
           return Objects.hashCode(key) ^ Objects.hashCode(value);
       }
     ```

     

     4.2 取模

     令 x = 1<<4，即 x 为 2 的 4 次方，它具有以下性质：

     ```java
       x   : 00010000
       x-1 : 00001111
     ```

     令一个数 y 与 x-1 做与运算，可以去除 y 位级表示的第 4 位以上数：

     ```java
       y       : 10110010
       x-1     : 00001111
       y&(x-1) : 00000010
     ```

     这个性质和 y 对 x 取模效果是一样的：

     ```java
       y   : 10110010
       x   : 00010000
       y%x : 00000010
     ```

     我们知道，位运算的代价比求模运算小的多，因此在进行这种计算时用位运算的话能带来更高的性能。

     确定桶下标的最后一步是将 key 的 hash 值对桶个数取模：hash%capacity，如果能保证 capacity 为 2 的 n 次方，那么就可以将这个操作转换为位运算。

     ```java
       static int indexFor(int h, int length) {
           return h & (length-1);
       }
     ```

     

  5. 扩容-基本原理

     设 HashMap 的 table 长度为 M，需要存储的键值对数量为 N，如果哈希函数满足均匀性的要求，那么每条链表的长度大约为 N/M，因此查找的复杂度为 O(N/M)。

     为了让查找的成本降低，应该使 N/M 尽可能小，因此需要保证 M 尽可能大，也就是说 table 要尽可能大。HashMap 采用动态扩容来根据当前的 N 值来调整 M 值，使得空间效率和时间效率都能得到保证。

     和扩容相关的参数主要有：capacity、size、threshold 和 load_factor。

     | 参数       | 含义                                                         |
     | ---------- | ------------------------------------------------------------ |
     | capacity   | table 的容量大小，默认为 16。需要注意的是 capacity 必须保证为 2 的 n 次方。 |
     | size       | 键值对数量。                                                 |
     | threshold  | size 的临界值，当 size 大于等于 threshold 就必须进行扩容操作。 |
     | loadFactor | 装载因子，table 能够使用的比例，threshold = (int)(capacity* loadFactor)。 |
     
     ```java
       static final int DEFAULT_INITIAL_CAPACITY = 16;
       
       static final int MAXIMUM_CAPACITY = 1 << 30;
       
       static final float DEFAULT_LOAD_FACTOR = 0.75f;
       
       transient Entry[] table;
       
       transient int size;
       
       int threshold;
       
       final float loadFactor;
       
       transient int modCount;
     ```
     
     从下面的添加元素代码中可以看出，当需要扩容时，令 capacity 为原来的两倍。
     
     ```java
       void addEntry(int hash, K key, V value, int bucketIndex) {
           Entry<K,V> e = table[bucketIndex];
           table[bucketIndex] = new Entry<>(hash, key, value, e);
           if (size++ >= threshold)
               resize(2 * table.length);
       }
     ```
     
     扩容使用 resize() 实现，需要注意的是，扩容操作同样需要把 oldTable 的所有键值对重新插入 newTable 中，因此这一步是很费时的。
     
     ```java
       void resize(int newCapacity) {
           Entry[] oldTable = table;
           int oldCapacity = oldTable.length;
           if (oldCapacity == MAXIMUM_CAPACITY) {
               threshold = Integer.MAX_VALUE;
               return;
           }
           Entry[] newTable = new Entry[newCapacity];
           transfer(newTable);
           table = newTable;
           threshold = (int)(newCapacity * loadFactor);
       }
       
       void transfer(Entry[] newTable) {
           Entry[] src = table;
           int newCapacity = newTable.length;
           for (int j = 0; j < src.length; j++) {
               Entry<K,V> e = src[j];
               if (e != null) {
                   src[j] = null;
                   do {
                       Entry<K,V> next = e.next;
                       int i = indexFor(e.hash, newCapacity);
                       e.next = newTable[i];
                       newTable[i] = e;
                       e = next;
                   } while (e != null);
               }
           }
       }
     ```
     
     
  
  6. 扩容-重新计算桶下标
  
     在进行扩容时，需要把键值对重新计算桶下标，从而放到对应的桶上。在前面提到，HashMap 使用 hash%capacity 来确定桶下标。HashMap capacity 为 2 的 n 次方这一特点能够极大降低重新计算桶下标操作的复杂度。
  
     假设原数组长度 capacity 为 16，扩容之后 new capacity 为 32：
  
     ```java
       capacity     : 00010000
       new capacity : 00100000
     ```
  
     对于一个 Key，它的哈希值 hash 在第 5 位：
  
     * 为 0，那么 hash%00010000 = hash%00100000，桶位置和原来一致；
  
     * 为 1，hash%00010000 = hash%00100000 + 16，桶位置是原位置 + 16。
  
       
  
  7. 计算数组容量
  
     HashMap 构造函数允许用户传入的容量不是 2 的 n 次方，因为它可以自动地将传入的容量转换为 2 的 n 次方。
  
     先考虑如何求一个数的掩码，对于 10010000，它的掩码为 11111111，可以使用以下方法得到：
  
     ```java
       mask |= mask >> 1    11011000
       mask |= mask >> 2    11111110
       mask |= mask >> 4    11111111
     ```
  
     mask+1 是大于原始数字的最小的 2 的 n 次方。
  
     ```java
       num     10010000
       mask+1 100000000
     ```
  
     以下是 HashMap 中计算数组容量的代码：
  
     ```java
       static final int tableSizeFor(int cap) {
           int n = cap - 1;
           n |= n >>> 1;
           n |= n >>> 2;
           n |= n >>> 4;
           n |= n >>> 8;
           n |= n >>> 16;
           return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
       }
     ```
  
     
  
  8. 链表转红黑树
  
     从 JDK 1.8 开始，一个桶存储的链表长度大于等于 8 时会将链表转换为红黑树。
  
  9. 与 Hashtable 的比较
     * Hashtable 使用 synchronized 来进行同步。
     * HashMap 可以插入键为 null 的 Entry。
     * HashMap 的迭代器是 fail-fast 迭代器。
     * HashMap 不能保证随着时间的推移 Map 中的元素次序是不变的。

  

* **ConcurrentHashMap**

  1. 存储结构

     <img src="https://camo.githubusercontent.com/7a6d1a946245fd7215a329f41ce9b182c468a3a974e0b5b39eba937d76e334c8/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f696d6167652d32303139313230393030313033383032342e706e67" alt="img"  />

     ```java
     static final class HashEntry<K,V> {
         final int hash;
         final K key;
         volatile V value;
         volatile HashEntry<K,V> next;
     }
     ```

     

     ConcurrentHashMap 和 HashMap 实现上类似，最主要的差别是 ConcurrentHashMap 采用了分段锁（Segment），每个分段锁维护着几个桶（HashEntry），多个线程可以同时访问不同分段锁上的桶，从而使其并发度更高（并发度就是 Segment 的个数）。

     Segment 继承自 ReentrantLock。

     ```java
     static final class Segment<K,V> extends ReentrantLock implements Serializable {
     
         private static final long serialVersionUID = 2249069246763182397L;
     
         static final int MAX_SCAN_RETRIES =
             Runtime.getRuntime().availableProcessors() > 1 ? 64 : 1;
     
         transient volatile HashEntry<K,V>[] table;
     
         transient int count;
     
         transient int modCount;
     
         transient int threshold;
     
         final float loadFactor;
     }
     final Segment<K,V>[] segments;
     ```

     默认的并发级别为 16，也就是说默认创建 16 个 Segment。

     ```java
     static final int DEFAULT_CONCURRENCY_LEVEL = 16;
     ```

     

  2. size 操作

     每个 Segment 维护了一个 count 变量来统计该 Segment 中的键值对个数。

     ```java
     /**
      * The number of elements. Accessed only either within locks
      * or among other volatile reads that maintain visibility.
      */
     transient int count;
     ```

     在执行 size 操作时，需要遍历所有 Segment 然后把 count 累计起来。

     ConcurrentHashMap 在执行 size 操作时先尝试不加锁，如果连续两次不加锁操作得到的结果一致，那么可以认为这个结果是正确的。

     尝试次数使用 RETRIES_BEFORE_LOCK 定义，该值为 2，retries 初始值为 -1，因此尝试次数为 3。

     如果尝试的次数超过 3 次，就需要对每个 Segment 加锁。

     ```java
     /**
      * Number of unsynchronized retries in size and containsValue
      * methods before resorting to locking. This is used to avoid
      * unbounded retries if tables undergo continuous modification
      * which would make it impossible to obtain an accurate result.
      */
     static final int RETRIES_BEFORE_LOCK = 2;
     
     public int size() {
         // Try a few times to get accurate count. On failure due to
         // continuous async changes in table, resort to locking.
         final Segment<K,V>[] segments = this.segments;
         int size;
         boolean overflow; // true if size overflows 32 bits
         long sum;         // sum of modCounts
         long last = 0L;   // previous sum
         int retries = -1; // first iteration isn't retry
         try {
             for (;;) {
                 // 超过尝试次数，则对每个 Segment 加锁
                 if (retries++ == RETRIES_BEFORE_LOCK) {
                     for (int j = 0; j < segments.length; ++j)
                         ensureSegment(j).lock(); // force creation
                 }
                 sum = 0L;
                 size = 0;
                 overflow = false;
                 for (int j = 0; j < segments.length; ++j) {
                     Segment<K,V> seg = segmentAt(segments, j);
                     if (seg != null) {
                         sum += seg.modCount;
                         int c = seg.count;
                         if (c < 0 || (size += c) < 0)
                             overflow = true;
                     }
                 }
                 // 连续两次得到的结果一致，则认为这个结果是正确的
                 if (sum == last)
                     break;
                 last = sum;
             }
         } finally {
             if (retries > RETRIES_BEFORE_LOCK) {
                 for (int j = 0; j < segments.length; ++j)
                     segmentAt(segments, j).unlock();
             }
         }
         return overflow ? Integer.MAX_VALUE : size;
     }
     ```

     

  3. JDK 1.8 的改动

     JDK 1.7 使用分段锁机制来实现并发更新操作，核心类为 Segment，它继承自重入锁 ReentrantLock，并发度与 Segment 数量相等。

     JDK 1.8 使用了 CAS 操作来支持更高的并发度，在 CAS 操作失败时使用内置锁 synchronized。并且 JDK 1.8 的实现也在链表过长时会转换为红黑树。



* **HashTable**：和 HashMap 类似，但它是线程安全的，这意味着同一时刻多个线程同时写入 HashTable 不会导致数据不一致。它是遗留类，不应该去使用它，而是使用 ConcurrentHashMap 来支持线程安全，ConcurrentHashMap 的效率会更高，因为 ConcurrentHashMap 引入了分段锁。

  

* **LinkedHashMap**：使用双向链表来维护元素的顺序，顺序为插入顺序或者最近最少使用（LRU）顺序。

  1. 存储结构

     继承自 HashMap，因此具有和 HashMap 一样的快速查找特性。

     ```java
     public class LinkedHashMap<K,V> extends HashMap<K,V> implements Map<K,V>
     ```

     内部维护了一个双向链表，用来维护插入顺序或者 LRU 顺序。

     ```java
     /**
      * The head (eldest) of the doubly linked list.
      */
     transient LinkedHashMap.Entry<K,V> head;
     
     /**
      * The tail (youngest) of the doubly linked list.
      */
     transient LinkedHashMap.Entry<K,V> tail;
     ```

     accessOrder 决定了顺序，默认为 false，此时维护的是插入顺序。

     ```java
     final boolean accessOrder;
     ```

     LinkedHashMap 最重要的是以下用于维护顺序的函数，它们会在 put、get 等方法中调用。

     ```java
     void afterNodeAccess(Node<K,V> p) { }
     void afterNodeInsertion(boolean evict) { }
     ```

     

  2. afterNodeAccess()

     当一个节点被访问时，如果 accessOrder 为 true，则会将该节点移到链表尾部。也就是说指定为 LRU 顺序之后，在每次访问一个节点时，会将这个节点移到链表尾部，保证链表尾部是最近访问的节点，那么链表首部就是最近最久未使用的节点。

     ```java
     void afterNodeAccess(Node<K,V> e) { // move node to last
         LinkedHashMap.Entry<K,V> last;
         if (accessOrder && (last = tail) != e) {
             LinkedHashMap.Entry<K,V> p =
                 (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
             p.after = null;
             if (b == null)
                 head = a;
             else
                 b.after = a;
             if (a != null)
                 a.before = b;
             else
                 last = b;
             if (last == null)
                 head = p;
             else {
                 p.before = last;
                 last.after = p;
             }
             tail = p;
             ++modCount;
         }
     }
     ```

     

  3. afterNodeInsertion()

     在 put 等操作之后执行，当 removeEldestEntry() 方法返回 true 时会移除最晚的节点，也就是链表首部节点 first。

     evict 只有在构建 Map 的时候才为 false，在这里为 true。

     ```java
     void afterNodeInsertion(boolean evict) { // possibly remove eldest
         LinkedHashMap.Entry<K,V> first;
         if (evict && (first = head) != null && removeEldestEntry(first)) {
             K key = first.key;
             removeNode(hash(key), key, null, false, true);
         }
     }
     ```

     removeEldestEntry() 默认为 false，如果需要让它为 true，需要继承 LinkedHashMap 并且覆盖这个方法的实现，这在实现 LRU 的缓存中特别有用，通过移除最近最久未使用的节点，从而保证缓存空间足够，并且缓存的数据都是热点数据。

     ```java
     protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
         return false;
     }
     ```

  4. LRU 缓存

     以下是使用 LinkedHashMap 实现的一个 LRU 缓存：

     * 设定最大缓存空间 MAX_ENTRIES 为 3；
     * 使用 LinkedHashMap 的构造函数将 accessOrder 设置为 true，开启 LRU 顺序；
     * 覆盖 removeEldestEntry() 方法实现，在节点多于 MAX_ENTRIES 就会将最近最久未使用的数据移除。

     ```java
     class LRUCache<K, V> extends LinkedHashMap<K, V> {
         private static final int MAX_ENTRIES = 3;
     
         protected boolean removeEldestEntry(Map.Entry eldest) {
             return size() > MAX_ENTRIES;
         }
     
         LRUCache() {
             super(MAX_ENTRIES, 0.75f, true);
         }
     }
     public static void main(String[] args) {
         LRUCache<Integer, String> cache = new LRUCache<>();
         cache.put(1, "a");
         cache.put(2, "b");
         cache.put(3, "c");
         cache.get(1);
         cache.put(4, "d");
         System.out.println(cache.keySet());
     }
     ```

     ```java
     [3, 1, 4]
     ```



### 迭代器

![](https://camo.githubusercontent.com/e44932dbf2dc015828f878428f58df99b9e808a6fb5cc76ab65b8baab7c0d2bb/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f696d6167652d32303139313230383232353330313937332e706e67)

Collection 继承了 Iterable 接口，其中的 iterator() 方法能够产生一个 Iterator 对象，通过这个对象就可以迭代遍历 Collection 中的元素。

从 JDK 1.5 之后可以使用 foreach 方法来遍历实现了 Iterable 接口的聚合对象。

```
List<String> list = new ArrayList<>();
list.add("a");
list.add("b");
for (String item : list) {
    System.out.println(item);
}
```



### 比较器 

在Java中有两个接口来实现Comparable和Comparator，每一个对象都有一个必须实现的接口。分别是：

1. java.lang.Comparable: int compareTo(Object o1)；这个方法用于当前对象与o1对象做对比，返回int值，分别的意思是：

   positive – 当前对象大于o1
   zero – 当前对象等于o1
   negative – 当前对象小于o1

2. java.util.Comparator: int compare(Object o1, Objecto2)；这个方法用于o1与o2对象做对比，返回int值，分别的意思是：
   * positive – o1大于o2
   * zero – o1等于o2
   * negative – o1小于o2

Comparable是排序接口；若一个类实现了Comparable接口，就意味着“该类支持排序”。而Comparator是比较器；我们若需要控制某个类的次序，可以建立一个“该类的比较器”来进行排序。我们不难发现：Comparable相当于“内部比较器”，而Comparator相当于“外部比较器”。



## Java IO / NIO / NIO.2



### Java IO模型

* 知识点

  * 五大IO模型

    * **阻塞IO模型**

      ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/9/165bde9e5723181b~tplv-t2oaga2asx-watermark.awebp)

    * **非阻塞IO模型**

      ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/9/165bde9e699d0713~tplv-t2oaga2asx-watermark.awebp)

    * **IO复用模型**

      ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/9/165bde9e6e6da275~tplv-t2oaga2asx-watermark.awebp)

    * **信号驱动IO模型**

      ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/9/165bde9e6c6552e2~tplv-t2oaga2asx-watermark.awebp)

    * **异步IO模型**

      ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/9/9/165bde9e70ade864~tplv-t2oaga2asx-watermark.awebp)

  * <font color=red>文件描述符</font>

  * select、poll，epoll

    * 

* 参考资料

  * [漫话：如何给女朋友解释什么是Linux的五种IO模型？](https://juejin.cn/post/6844903687626686472)
  * [Linux IO模式及 select、poll、epoll详解](https://juejin.cn/post/6844903488170786824#heading-15)



### Java IO

* 知识点

  * [一、概览](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#一概览)

    ![分类帮助记忆](https://user-gold-cdn.xitu.io/2018/5/14/1635daccc4d2bce3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

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
  * **流与块**

    可简单认为：**IO是面向流的处理，NIO是面向块(缓冲区)的处理**

    - 面向流的I/O 系统**一次一个字节地处理数据**。
    - 一个面向块(缓冲区)的I/O系统**以块的形式处理数据**。

  * **NIO的三个核心组成部分**

    1. **buffer缓冲区 与 Channel通道**

       简单理解，Channel管道比作成铁路，buffer缓冲区比作成火车(运载着货物。Channel不与数据打交道，它只负责运输数据。与数据打交道的是Buffer缓冲区

       - **Channel-->运输，双向的** 
       - **Buffer-->数据**

       

       1.1 缓冲区实质上是一个数组，但它不仅仅是一个数组。缓冲区提供了对数据的结构化访问，而且还可以跟踪系统的读/写进程。

       缓冲区包括以下类型：

       - ByteBuffer
       - CharBuffer
       - ShortBuffer
       - IntBuffer
       - LongBuffer
       - FloatBuffer
       - DoubleBuffer

       

       Buffer类维护了4个核心变量属性来提供**关于其所包含的数组的信息**。它们是：

       - 容量Capacity

         **缓冲区能够容纳的数据元素的最大数量**。容量在缓冲区创建时被设定，并且永远不能被改变。(不能被改变的原因也很简单，底层是数组嘛)

       - 上界Limit

         **缓冲区里的数据的总数**，代表了当前缓冲区中一共有多少数据。

       - 位置Position

         **下一个要被读或写的元素的位置**。Position会自动由相应的 `get( )`和 `put( )`函数更新。

       - 标记Mark

         一个备忘位置。**用于记录上一次读写的位置**。


       1.2 通道 Channel 是对原 I/O 包中的流的模拟，可以通过它读取和写入数据。

       通道与流的不同之处在于，流只能在一个方向上移动(一个流必须是 InputStream 或者 OutputStream 的子类)，而通道是双向的，可以用于读、写或者同时用于读写。

       通道包括以下类型：

       - FileChannel：从文件中读写数据；
       - DatagramChannel：通过 UDP 读写网络中数据；
       - SocketChannel：通过 TCP 读写网络中数据；
       - ServerSocketChannel：可以监听新进来的 TCP 连接，对每一个新进来的连接都会创建一个 SocketChannel。

       

       FileChannel通道的核心要点

       1. 使用**FileChannel配合缓冲区**实现文件复制的功能
       2. 使用**内存映射文件**的方式实现**文件复制**的功能(直接操作缓冲区)
       3. 通道之间通过`transfer()`实现数据的传输(直接操作缓冲区)

       

    2. **Selector选择器**

       NIO 常常被叫做非阻塞 IO，主要是因为 NIO 在网络通信中的非阻塞特性被广泛使用。

       NIO 实现了 IO 多路复用中的 Reactor 模型，一个线程 Thread 使用一个选择器 Selector 通过轮询的方式去监听多个通道 Channel 上的事件，从而让一个线程就可以处理多个事件。

       通过配置监听的通道 Channel 为非阻塞，那么当 Channel 上的 IO 事件还未到达时，就不会进入阻塞状态一直等待，而是继续轮询其它 Channel，找到 IO 事件已经到达的 Channel 执行。

       因为创建和切换线程的开销很大，因此使用一个线程来处理多个事件而不是一个线程处理一个事件，对于 IO 密集型的应用具有很好地性能。

       应该注意的是，只有套接字 Channel 才能配置为非阻塞，而 FileChannel 不能，为 FileChannel 配置非阻塞也没有意义。

       [![img](https://camo.githubusercontent.com/4eec44e1b8f88753fa417f39fbf92050179499c0f08513e2f7d50a051aad1f32/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f30393366396535372d343239632d343133612d383365652d6336383962613539366365662e706e67)](https://camo.githubusercontent.com/4eec44e1b8f88753fa417f39fbf92050179499c0f08513e2f7d50a051aad1f32/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f30393366396535372d343239632d343133612d383365652d6336383962613539366365662e706e67)

       

       #### 1. 创建选择器

       ```
       Selector selector = Selector.open();
       ```

       #### 2. 将通道注册到选择器上

       ```
       ServerSocketChannel ssChannel = ServerSocketChannel.open();
       ssChannel.configureBlocking(false);
       ssChannel.register(selector, SelectionKey.OP_ACCEPT);
       ```

       通道必须配置为非阻塞模式，否则使用选择器就没有任何意义了，因为如果通道在某个事件上被阻塞，那么服务器就不能响应其它事件，必须等待这个事件处理完毕才能去处理其它事件，显然这和选择器的作用背道而驰。

       在将通道注册到选择器上时，还需要指定要注册的具体事件，主要有以下几类：

       - SelectionKey.OP_CONNECT
       - SelectionKey.OP_ACCEPT
       - SelectionKey.OP_READ
       - SelectionKey.OP_WRITE

       它们在 SelectionKey 的定义如下：

       ```
       public static final int OP_READ = 1 << 0;
       public static final int OP_WRITE = 1 << 2;
       public static final int OP_CONNECT = 1 << 3;
       public static final int OP_ACCEPT = 1 << 4;
       ```

       可以看出每个事件可以被当成一个位域，从而组成事件集整数。例如：

       ```
       int interestSet = SelectionKey.OP_READ | SelectionKey.OP_WRITE;
       ```

       #### 3. 监听事件

       ```
       int num = selector.select();
       ```

       使用 select() 来监听到达的事件，它会一直阻塞直到有至少一个事件到达。

       #### 4. 获取到达的事件

       ```
       Set<SelectionKey> keys = selector.selectedKeys();
       Iterator<SelectionKey> keyIterator = keys.iterator();
       while (keyIterator.hasNext()) {
           SelectionKey key = keyIterator.next();
           if (key.isAcceptable()) {
               // ...
           } else if (key.isReadable()) {
               // ...
           }
           keyIterator.remove();
       }
       ```

       #### 5. 事件循环

       因为一次 select() 调用不能处理完所有的事件，并且服务器端有可能需要一直监听事件，因此服务器端处理事件的代码一般会放在一个死循环内。

       ```
       while (true) {
           int num = selector.select();
           Set<SelectionKey> keys = selector.selectedKeys();
           Iterator<SelectionKey> keyIterator = keys.iterator();
           while (keyIterator.hasNext()) {
               SelectionKey key = keyIterator.next();
               if (key.isAcceptable()) {
                   // ...
               } else if (key.isReadable()) {
                   // ...
               }
               keyIterator.remove();
           }
       }
       ```

    

  * [文件 NIO 实例](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#文件-nio-实例)

  * [套接字 NIO 实例](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#套接字-nio-实例)

  * [内存映射文件](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java IO.md#内存映射文件)

* 参考资料
  
  * [JDK10都发布了，nio你了解多少？](https://juejin.cn/post/6844903605669986317#heading-0)



### Java 文件 I/O(采用 NIO.2)

* 知识点
  * Path类 - 
  * Files类 - 
  * 随机访问文件 -  SeekableByteChannel 
  * FileVisitor，使用Files.walkFileTree(Path, FileVisitor)遍历文件树 
* 参考资料
  * [文件 I/O(采用 NIO.2) - Java Tutorials 中文版](https://pingfangx.github.io/java-tutorials/essential/io/fileio.html)



### 编码与解码

编码就是把字符转换为字节，而解码是把字节重新组合成字符。

如果编码和解码过程使用不同的编码方式那么就出现了乱码。

- GBK 编码中，中文字符占 2 个字节，英文字符占 1 个字节；
- UTF-8 编码中，中文字符占 3 个字节，英文字符占 1 个字节；
- UTF-16be 编码中，中文字符和英文字符都占 2 个字节。

UTF-16be 中的 be 指的是 Big Endian，也就是大端。相应地也有 UTF-16le，le 指的是 Little Endian，也就是小端。

Java 的内存编码使用双字节编码 UTF-16be，这不是指 Java 只支持这一种编码方式，而是说 char 这种类型使用 UTF-16be 进行编码。char 类型占 16 位，也就是两个字节，Java 使用这种双字节编码是为了让一个中文或者一个英文都能使用一个 char 来存储。



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