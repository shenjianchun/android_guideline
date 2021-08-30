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

      - getDeclaredField(String name)   

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

         - getDeclaredMethod(String name, Class<?>... parameterTypes)

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
         > - 返回值为普通简单类型如 Object, int, String 等，getGenericReturnType() 返回值和 getReturnType() 一样，例如 public String function1()，那么各自返回值为：
         >
         >   - getReturnType() : class java.lang.String
         >   - getGenericReturnType() : class java.lang.String
         >
         > - 返回值为泛型，例如 public T function2()，那么各自返回值为：
         >
         >   - getReturnType() : class java.lang.Object
         >   - getGenericReturnType() : T
         >
         > - 返回值为参数化类型
         >
         >   例如public Class<String> function3()，那么各自返回值为：
         >
         >   * getReturnType() : class java.lang.Class
         >
         >   - getGenericReturnType() : java.lang.Class<java.lang.String>
         >
         > **其实反射中所有形如 getGenericXXX()的方法规则都与上面所述类似。**

         

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

* 知识点

  Throwable 可以用来表示任何可以作为异常抛出的类，分为两种： **Error** 和 **Exception**。其中 Error 用来表示 JVM 无法处理的错误，Exception 分为两种：

  * **受检异常** ：需要用 try...catch... 语句捕获并进行处理，并且可以从异常中恢复；
  * **非受检异常（运行时）** ：是程序运行时错误，例如除 0 会引发 Arithmetic Exception，此时程序崩溃并且无法恢复。

  ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/8/10/165242e52c2beee2~tplv-t2oaga2asx-watermark.awebp)



* 参考
  * [Java提高篇——Java 异常处理](https://www.cnblogs.com/Qian123/p/5715402.html)
  * [深入理解Java异常](https://juejin.cn/post/6844903657478029326#heading-0)

### [泛型](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 基础.md#九泛型)

* 知识点

  1. **泛型的概述**

     * 泛型的由来

       根据《Java编程思想》中的描述，泛型出现的动机：有很多原因促成了泛型的出现，而最引人注意的一个原因，就是为了创建容器类。

     * 基本概述

       - 泛型的本质就是"参数化类型"。一提到参数，最熟悉的就是定义方法的时候需要形参，调用方法的时候，需要传递实参。那"参数化类型"就是将原来具体的类型参数化
       - 泛型的出现避免了强转的操作，在编译器完成类型转化，也就避免了运行的错误。

  2. **泛型的使用**

     泛型的三种使用方式：**泛型类**，**泛型方法**，**泛型接口**

     2.1 泛型类

     * 泛型类概述：把泛型定义在类上
     * 定义格式：

     ```java
     public class 类名 <泛型类型1,...> {
         
     }
     ```

     * 注意事项：泛型类型必须是引用类型（非基本数据类型）

       

     2.2 泛型方法

     - 泛型方法概述：把泛型定义在方法上
     - 定义格式：

     ```java
     public <泛型类型> 返回类型 方法名（泛型类型 变量名） {
         
     }
     ```

     - 注意要点：

       方法声明中定义的形参只能在该方法里使用，而接口、类声明中定义的类型形参则可以在整个接口、类中使用。当调用fun()方法时，根据传入的实际对象，编译器就会判断出类型形参T所代表的实际类型。

     

     2.3 泛型接口

     - 泛型接口概述：把泛型定义在接口
     - 定义格式：

     ```java
     public interface 接口名<泛型类型> {
         
     }
     ```

     

     2.5 泛型类派生子类

     **父类派生子类的时候不能在包含类型形参，需要传入具体的类型**

     - 错误的方式：

     ```
     public class A extends Container<K, V> {}
     ```

     - 正确的方式：

     ```
     public class A extends Container<Integer, String> {}
     ```

     - 也可以不指定具体的类型，系统就会把K,V形参当成Object类型处理

     ```
     public class A extends Container {}
     ```

     

     2.6 高级通配符

     * <? extends T> 上界通配符

       上界通配符顾名思义，<? extends T>表示的是类型的上界【包含自身】，因此通配的参数化类型可能是T或T的子类。

       - 正因为无法确定具体的类型是什么，add方法受限（可以添加null，因为null表示任何类型），但可以从列表中获取元素后赋值给父类型。如上图中的第一个例子，第三个add()操作会受限，原因在于List和List是List<? extends Animal>的子类型。

       ```
       它表示集合中的所有元素都是Animal类型或者其子类
        List<? extends Animal>
       ```

       这就是所谓的上限通配符，使用关键字extends来实现，实例化时，指定类型实参只能是extends后类型的子类或其本身。

       - 例如：
       - 这样就确定集合中元素的类型，虽然不确定具体的类型，但最起码知道其父类。然后进行其他操作。

       ```
       //Cat是其子类
           List<? extends Animal> list = new ArrayList<Cat>();
       ```

       

     * <? super T> 下界通配符

       下界通配符<? super T>表示的是参数化类型是T的超类型（包含自身），层层至上，直至Object

       - 编译器无从判断get()返回的对象的类型是什么，因此get()方法受限。但是可以进行add()方法，add()方法可以添加T类型和T类型的子类型，如第二个例子中首先添加了一个Cat类型对象，然后添加了两个Cat子类类型的对象，这种方法是可行的，但是如果添加一个Animal类型的对象，显然将继承的关系弄反了，是不可行的。

       ```
        它表示集合中的所有元素都是Cat类型或者其父类
        List <? super Cat>   
       ```

       这就是所谓的下限通配符，使用关键字super来实现，实例化时，指定类型实参只能是extends后类型的子类或其本身

       - 例如

       ```
       //Animal是其父类
       List<? super Cat> list = new ArrayList<Animal>();
       ```

       

     * <?> 无界通配符

       任意类型，如果没有明确，那么就是Object以及任意的Java类了

       无界通配符用<?>表示，?代表了任何的一种类型，能代表任何一种类型的只有null（Object本身也算是一种类型，但却不能代表任何一种类型，所以List<>和List的含义是不同的，前者类型是Object，也就是继承树的最上层，而后者的类型完全是未知的）

     

  3. **泛型擦除**

     编译器编译带类型说明的集合时会去掉类型信息。

     ```java
     public class GenericTest {
         public static void main(String[] args) {
             new GenericTest().testType();
         }
     
         public void testType(){
             ArrayList<Integer> collection1 = new ArrayList<Integer>();
             ArrayList<String> collection2= new ArrayList<String>();
             
             System.out.println(collection1.getClass()==collection2.getClass());
             //两者class类型一样,即字节码一致
             
             System.out.println(collection2.getClass().getName());
             //class均为java.util.ArrayList,并无实际类型参数信息
         }
     }
     复制代码
     ```

     - 输出结果：

     ```
     true
     java.util.ArrayList
     ```

     - 分析：

       - 这是因为不管为泛型的类型形参传入哪一种类型实参，对于Java来说，它们依然被当成同一类处理，在内存中也只占用一块内存空间。从Java泛型这一概念提出的目的来看，其只是作用于代码编译阶段，在编译过程中，对于正确检验泛型结果后，会将泛型的相关信息擦出，也就是说，成功编译过后的class文件中是不包含任何泛型信息的。泛型信息不会进入到运行时阶段。

       - **在静态方法、静态初始化块或者静态变量的声明和初始化中不允许使用类型形参。由于系统中并不会真正生成泛型类，所以instanceof运算符后不能使用泛型类**

         

  4. **泛型与反射**

     - 把泛型变量当成方法的参数，利用Method类的getGenericParameterTypes方法来获取泛型的实际类型参数
     - 例子：

     ```
     public class GenericTest {
     
         public static void main(String[] args) throws Exception {
             getParamType();
         }
         
          /*利用反射获取方法参数的实际参数类型*/
         public static void getParamType() throws NoSuchMethodException{
             Method method = GenericTest.class.getMethod("applyMap",Map.class);
             //获取方法的泛型参数的类型
             Type[] types = method.getGenericParameterTypes();
             System.out.println(types[0]);
             //参数化的类型
             ParameterizedType pType  = (ParameterizedType)types[0];
             //原始类型
             System.out.println(pType.getRawType());
             //实际类型参数
             System.out.println(pType.getActualTypeArguments()[0]);
             System.out.println(pType.getActualTypeArguments()[1]);
         }
     
         /*供测试参数类型的方法*/
         public static void applyMap(Map<Integer,String> map){
     
         }
     }
     复制代码
     ```

     - 输出结果：

     ```
     java.util.Map<java.lang.Integer, java.lang.String>
     interface java.util.Map
     class java.lang.Integer
     class java.lang.String
     复制代码
     ```

     - 通过反射绕开编译器对泛型的类型限制

     ```
     public static void main(String[] args) throws Exception {
     		//定义一个包含int的链表
     		ArrayList<Integer> al = new ArrayList<Integer>();
     		al.add(1);
     		al.add(2);
     		//获取链表的add方法，注意这里是Object.class，如果写int.class会抛出NoSuchMethodException异常
     		Method m = al.getClass().getMethod("add", Object.class);
     		//调用反射中的add方法加入一个string类型的元素，因为add方法的实际参数是Object
     		m.invoke(al, "hello");
     		System.out.println(al.get(2));
     	}
     ```

     

  5. **泛型的限制**

     * 模糊性错误

       对于泛型类User<K,V>而言，声明了两个泛型类参数。在类中根据不同的类型参数重载show方法。

       ```
       public class User<K, V> {
           
           public void show(K k) { // 报错信息：'show(K)' clashes with 'show(V)'; both methods have same erasure
               
           }
           public void show(V t) {
       
           }
       }
       复制代码
       ```

       由于泛型擦除，二者本质上都是Obejct类型。方法是一样的，所以编译器会报错。

       换一个方式：

       ```
       public class User<K, V> {
       
           public void show(String k) {
       
           }
           public void show(V t) {
       
           }
       }
       ```

       

     * 不能实例化类型参数

       编译器也不知道该创建那种类型的对象

       ```
       public class User<K, V> {
       
           private K key = new K(); // 报错：Type parameter 'K' cannot be instantiated directly
       
       }
       ```

       

     * 对静态成员的限制

       静态方法无法访问类上定义的泛型；如果静态方法操作的类型不确定，必须要将泛型定义在方法上。

       **如果静态方法要使用泛型的话，必须将静态方法定义成泛型方法**。

       ```
       public class User<T> {
       
           //错误
           private static T t;
       
           //错误
           public static T getT() {
               return t;
           }
       
           //正确
           public static <K> void test(K k) {
       
           }
       }
       ```

       

     * 对泛型数组的限制

       不能实例化元素类型为类型参数的数组，但是可以将数组指向类型兼容的数组的引用

       ```
       public class User<T> {
       
           private T[] values;
       
           public User(T[] values) {
               //错误，不能实例化元素类型为类型参数的数组
               this.values = new T[5];
               //正确，可以将values 指向类型兼容的数组的引用
               this.values = values;
           }
       }
       ```


​       

     * 对泛型异常的限制
    
       泛型类不能扩展 Throwable，意味着不能创建泛型异常类 [答案链接](https://link.juejin.cn/?target=https%3A%2F%2Fstackoverflow.com%2Fquestions%2F501277%2Fwhy-doesnt-java-allow-generic-subclasses-of-throwable)

* 参考资料

  * [java 泛型全解 - 绝对最详细](https://juejin.cn/post/6844903925666021389)
  * [Java泛型详解](https://www.cnblogs.com/Blue-Keroro/p/8875898.html)



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


​       

       FileChannel通道的核心要点
    
       1. 使用**FileChannel配合缓冲区**实现文件复制的功能
       2. 使用**内存映射文件**的方式实现**文件复制**的功能(直接操作缓冲区)
       3. 通道之间通过`transfer()`实现数据的传输(直接操作缓冲区)


​       

    2. **Selector选择器**
    
       NIO 常常被叫做非阻塞 IO，主要是因为 NIO 在网络通信中的非阻塞特性被广泛使用。
    
       NIO 实现了 IO 多路复用中的 Reactor 模型，一个线程 Thread 使用一个选择器 Selector 通过轮询的方式去监听多个通道 Channel 上的事件，从而让一个线程就可以处理多个事件。
    
       通过配置监听的通道 Channel 为非阻塞，那么当 Channel 上的 IO 事件还未到达时，就不会进入阻塞状态一直等待，而是继续轮询其它 Channel，找到 IO 事件已经到达的 Channel 执行。
    
       因为创建和切换线程的开销很大，因此使用一个线程来处理多个事件而不是一个线程处理一个事件，对于 IO 密集型的应用具有很好地性能。
    
       应该注意的是，只有套接字 Channel 才能配置为非阻塞，而 FileChannel 不能，为 FileChannel 配置非阻塞也没有意义。
    
       [![img](https://camo.githubusercontent.com/4eec44e1b8f88753fa417f39fbf92050179499c0f08513e2f7d50a051aad1f32/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f30393366396535372d343239632d343133612d383365652d6336383962613539366365662e706e67)](https://camo.githubusercontent.com/4eec44e1b8f88753fa417f39fbf92050179499c0f08513e2f7d50a051aad1f32/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f30393366396535372d343239632d343133612d383365652d6336383962613539366365662e706e67)


​       

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


​    

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
  - **Executor**

    Executor 管理多个异步任务的执行，而无需程序员显式地管理线程的生命周期。这里的异步是指多个任务的执行互不干扰，不需要进行同步操作。

    主要有三种 Executor：

    - CachedThreadPool：一个任务创建一个线程；
    - FixedThreadPool：所有任务只能使用固定大小的线程；
    - SingleThreadExecutor：相当于大小为 1 的 FixedThreadPool。

    ```java
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < 5; i++) {
            executorService.execute(new MyRunnable());
        }
        executorService.shutdown();
    }
    ```

    

  - Daemon

    守护线程是程序运行时在后台提供服务的线程，不属于程序中不可或缺的部分。

    当所有非守护线程结束时，程序也就终止，同时会杀死所有守护线程。

    main() 属于非守护线程。

    在线程启动之前使用 setDaemon() 方法可以将一个线程设置为守护线程。

    ```java
    public static void main(String[] args) {
        Thread thread = new Thread(new MyRunnable());
        thread.setDaemon(true);
    }
    ```

    

  - sleep()

    Thread.sleep(millisec) 方法会休眠当前正在执行的线程，millisec 单位为毫秒。

    sleep() 可能会抛出 InterruptedException，因为异常不能跨线程传播回 main() 中，因此必须在本地进行处理。线程中抛出的其它异常也同样需要在本地进行处理。

    ```java
    public void run() {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    ```

    

  - yield()

    对静态方法 Thread.yield() 的调用声明了当前线程已经完成了生命周期中最重要的部分，可以切换给其它线程来执行。该方法只是对线程调度器的一个建议，而且也只是建议具有相同优先级的其它线程可以运行。

    ```java
    public void run() {
        Thread.yield();
    }
    ```



### 中断

* 知识点

  一个线程执行完毕之后会自动结束，如果在运行过程中发生异常也会提前结束。
  
  - **InterruptedException**

    通过调用一个线程的 interrupt() 来中断该线程，如果该线程处于阻塞、限期等待或者无限期等待状态，那么就会抛出 InterruptedException，从而提前结束该线程。但是不能中断 I/O 阻塞和 synchronized 锁阻塞。
  
    对于以下代码，在 main() 中启动一个线程之后再中断它，由于线程中调用了 Thread.sleep() 方法，因此会抛出一个 InterruptedException，从而提前结束线程，不执行之后的语句。
  
    ```java
    public class InterruptExample {
    
        private static class MyThread1 extends Thread {
            @Override
            public void run() {
                try {
                    Thread.sleep(2000);
                    System.out.println("Thread run");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Thread thread1 = new MyThread1();
        thread1.start();
        thread1.interrupt();
        System.out.println("Main run");
    }
    
    
    Main run
    java.lang.InterruptedException: sleep interrupted
        at java.lang.Thread.sleep(Native Method)
        at InterruptExample.lambda$main$0(InterruptExample.java:5)
        at InterruptExample$$Lambda$1/713338599.run(Unknown Source)
        at java.lang.Thread.run(Thread.java:745)
    ```
  
    
  
  - **interrupted()**
  
    如果一个线程的 run() 方法执行一个无限循环，并且没有执行 sleep() 等会抛出 InterruptedException 的操作，那么调用线程的 interrupt() 方法就无法使线程提前结束。
  
    但是调用 interrupt() 方法会设置线程的中断标记，此时调用 interrupted() 方法会返回 true。因此可以在循环体中使用 interrupted() 方法来判断线程是否处于中断状态，从而提前结束线程。
  
    ```java
    public class InterruptExample {
    
        private static class MyThread2 extends Thread {
            @Override
            public void run() {
                while (!interrupted()) {
                    // ..
                }
                System.out.println("Thread end");
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Thread thread2 = new MyThread2();
        thread2.start();
        thread2.interrupt();
    }
    
    
    Thread end
    ```
  
    
  
  - **Executor 的中断操作**
  
    调用 Executor 的 shutdown() 方法会等待线程都执行完毕之后再关闭，但是如果调用的是 shutdownNow() 方法，则相当于调用每个线程的 interrupt() 方法。
  
    以下使用 Lambda 创建线程，相当于创建了一个匿名内部线程。
  
    ```java
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newCachedThreadPool();
        executorService.execute(() -> {
            try {
                Thread.sleep(2000);
                System.out.println("Thread run");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        executorService.shutdownNow();
        System.out.println("Main run");
    }
    
    
    Main run
    java.lang.InterruptedException: sleep interrupted
        at java.lang.Thread.sleep(Native Method)
        at ExecutorInterruptExample.lambda$main$0(ExecutorInterruptExample.java:9)
        at ExecutorInterruptExample$$Lambda$1/1160460865.run(Unknown Source)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)
    ```
  
    如果只想中断 Executor 中的一个线程，可以通过使用 submit() 方法来提交一个线程，它会返回一个 Future<?> 对象，通过调用该对象的 cancel(true) 方法就可以中断线程。
  
    ```java
    Future<?> future = executorService.submit(() -> {
        // ..
    });
    future.cancel(true);
    ```
  
    
  

### [互斥同步](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#四互斥同步)
* 知识点

  Java 提供了两种锁机制来控制多个线程对共享资源的互斥访问，第一个是 JVM 实现的 synchronized，而另一个是 JDK 实现的 ReentrantLock。
  
  - synchronized
  
    **1. 同步一个代码块**
  
    ```
    public void func() {
        synchronized (this) {
            // ...
        }
    }
    ```
  
    它只作用于同一个对象，如果调用两个对象上的同步代码块，就不会进行同步。
  
    对于以下代码，使用 ExecutorService 执行了两个线程，由于调用的是同一个对象的同步代码块，因此这两个线程会进行同步，当一个线程进入同步语句块时，另一个线程就必须等待。
  
    ```
    public class SynchronizedExample {
    
        public void func1() {
            synchronized (this) {
                for (int i = 0; i < 10; i++) {
                    System.out.print(i + " ");
                }
            }
        }
    }
    public static void main(String[] args) {
        SynchronizedExample e1 = new SynchronizedExample();
        ExecutorService executorService = Executors.newCachedThreadPool();
        executorService.execute(() -> e1.func1());
        executorService.execute(() -> e1.func1());
    }
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9
    ```
  
    对于以下代码，两个线程调用了不同对象的同步代码块，因此这两个线程就不需要同步。从输出结果可以看出，两个线程交叉执行。
  
    ```
    public static void main(String[] args) {
        SynchronizedExample e1 = new SynchronizedExample();
        SynchronizedExample e2 = new SynchronizedExample();
        ExecutorService executorService = Executors.newCachedThreadPool();
        executorService.execute(() -> e1.func1());
        executorService.execute(() -> e2.func1());
    }
    0 0 1 1 2 2 3 3 4 4 5 5 6 6 7 7 8 8 9 9
    ```
  
    **2. 同步一个方法**
  
    ```
    public synchronized void func () {
        // ...
    }
    ```
  
    它和同步代码块一样，作用于同一个对象。
  
    **3. 同步一个类**
  
    ```
    public void func() {
        synchronized (SynchronizedExample.class) {
            // ...
        }
    }
    ```
  
    作用于整个类，也就是说两个线程调用同一个类的不同对象上的这种同步语句，也会进行同步。
  
    ```
    public class SynchronizedExample {
    
        public void func2() {
            synchronized (SynchronizedExample.class) {
                for (int i = 0; i < 10; i++) {
                    System.out.print(i + " ");
                }
            }
        }
    }
    
    public static void main(String[] args) {
        SynchronizedExample e1 = new SynchronizedExample();
        SynchronizedExample e2 = new SynchronizedExample();
        ExecutorService executorService = Executors.newCachedThreadPool();
        executorService.execute(() -> e1.func2());
        executorService.execute(() -> e2.func2());
    }
    
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9
    ```
  
    **4. 同步一个静态方法**
  
    ```
    public synchronized static void fun() {
        // ...
    }
    ```
  
    作用于整个类。
  
    
  
  - ReentrantLock
  
    ReentrantLock 是 java.util.concurrent（J.U.C）包中的锁。
  
    ```java
    public class LockExample {
    
        private Lock lock = new ReentrantLock();
    
        public void func() {
            lock.lock();
            try {
                for (int i = 0; i < 10; i++) {
                    System.out.print(i + " ");
                }
            } finally {
                lock.unlock(); // 确保释放锁，从而避免发生死锁。
            }
        }
    }
    
    public static void main(String[] args) {
        LockExample lockExample = new LockExample();
        ExecutorService executorService = Executors.newCachedThreadPool();
        executorService.execute(() -> lockExample.func());
        executorService.execute(() -> lockExample.func());
    }
    
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9
    ```
  
    
  
  - 比较
  
    **1. 锁的实现**
  
    synchronized 是 JVM 实现的，而 ReentrantLock 是 JDK 实现的。
  
    **2. 性能**
  
    新版本 Java 对 synchronized 进行了很多优化，例如自旋锁等，synchronized 与 ReentrantLock 大致相同。
  
    **3. 等待可中断**
  
    当持有锁的线程长期不释放锁的时候，正在等待的线程可以选择放弃等待，改为处理其他事情。
  
    ReentrantLock 可中断，而 synchronized 不行。
  
    **4. 公平锁**
  
    公平锁是指多个线程在等待同一个锁时，必须按照申请锁的时间顺序来依次获得锁。
  
    synchronized 中的锁是非公平的，ReentrantLock 默认情况下也是非公平的，但是也可以是公平的。
  
    **5. 锁绑定多个条件**
  
    一个 ReentrantLock 可以同时绑定多个 Condition 对象。
  
  
  
  - 使用选择
  
    除非需要使用 ReentrantLock 的高级功能，否则优先使用 synchronized。这是因为 synchronized 是 JVM 实现的一种锁机制，JVM 原生地支持它，而 ReentrantLock 不是所有的 JDK 版本都支持。并且使用 synchronized 不用担心没有释放锁而导致死锁问题，因为 JVM 会确保锁的释放。
  
* 参考资料

  * [ReentrantLock 重入锁(互斥锁)](https://blog.csdn.net/lm1060891265/article/details/81747275)



### [线程之间的协作](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#五线程之间的协作)

* 知识点
  
  当多个线程可以一起工作去解决某个问题时，如果某些部分必须在其它部分之前完成，那么就需要对线程进行协调。
  
  - **join()**
  
    在线程中调用另一个线程的 join() 方法，会将当前线程挂起，而不是忙等待，直到目标线程结束。
  
    对于以下代码，虽然 b 线程先启动，但是因为在 b 线程中调用了 a 线程的 join() 方法，b 线程会等待 a 线程结束才继续执行，因此最后能够保证 a 线程的输出先于 b 线程的输出。
  
    ```
    public class JoinExample {
    
        private class A extends Thread {
            @Override
            public void run() {
                System.out.println("A");
            }
        }
    
        private class B extends Thread {
    
            private A a;
    
            B(A a) {
                this.a = a;
            }
    
            @Override
            public void run() {
                try {
                    a.join();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("B");
            }
        }
    
        public void test() {
            A a = new A();
            B b = new B(a);
            b.start();
            a.start();
        }
    }
    ```
  
    
  
    ```
    public static void main(String[] args) {
        JoinExample example = new JoinExample();
        example.test();
    }
    
    A
    B
    ```
  
    
  
  - **wait() notify() notifyAll()**
  
    调用 wait() 使得线程等待某个条件满足，线程在等待时会被挂起，当其他线程的运行使得这个条件满足时，其它线程会调用 notify() 或者 notifyAll() 来唤醒挂起的线程。
  
    它们都属于 Object 的一部分，而不属于 Thread。
  
    只能用在同步方法或者同步控制块中使用，否则会在运行时抛出 IllegalMonitorStateException。
  
    使用 wait() 挂起期间，线程会释放锁。这是因为，如果没有释放锁，那么其它线程就无法进入对象的同步方法或者同步控制块中，那么就无法执行 notify() 或者 notifyAll() 来唤醒挂起的线程，造成死锁。
  
    ```java
    public class WaitNotifyExample {
    
        public synchronized void before() {
            System.out.println("before");
            notifyAll();
        }
    
        public synchronized void after() {
            try {
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("after");
        }
    }
    ```
  
    ```java
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newCachedThreadPool();
        WaitNotifyExample example = new WaitNotifyExample();
        executorService.execute(() -> example.after());
        executorService.execute(() -> example.before());
    }
    
    before
    after
    ```
  
    
  
    **wait() 和 sleep() 的区别**
  
    - wait() 是 Object 的方法，而 sleep() 是 Thread 的静态方法；
    - wait() 会释放锁，sleep() 不会。
  
    
  
  - **await() signal() signalAll()**
  
    java.util.concurrent 类库中提供了 Condition 类来实现线程之间的协调，可以在 Condition 上调用 await() 方法使线程等待，其它线程调用 signal() 或 signalAll() 方法唤醒等待的线程。
  
    相比于 wait() 这种等待方式，await() 可以指定等待的条件，因此更加灵活。
  
    使用 Lock 来获取一个 Condition 对象。
  
    ```java
    public class AwaitSignalExample {
    
        private Lock lock = new ReentrantLock();
        private Condition condition = lock.newCondition();
    
        public void before() {
            lock.lock();
            try {
                System.out.println("before");
                condition.signalAll();
            } finally {
                lock.unlock();
            }
        }
    
        public void after() {
            lock.lock();
            try {
                condition.await();
                System.out.println("after");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        }
    }
    ```
  
    ```java
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newCachedThreadPool();
        AwaitSignalExample example = new AwaitSignalExample();
        executorService.execute(() -> example.after());
        executorService.execute(() -> example.before());
    }
    
    before
    after
    ```
  
    

### [线程状态](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#六线程状态)

* 知识点

  ![线程状态图](https://img-blog.csdnimg.cn/20181120173640764.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3BhbmdlMTk5MQ==,size_16,color_FFFFFF,t_70)

  

  一个线程只能处于一种状态，并且这里的线程状态特指 Java 虚拟机的线程状态，不能反映线程在特定操作系统下的状态。

  ### 新建（NEW）

  创建后尚未启动。

  ### 可运行（RUNABLE）

  正在 Java 虚拟机中运行。但是在操作系统层面，它可能处于运行状态，也可能等待资源调度（例如处理器资源），资源调度完成就进入运行状态。所以该状态的可运行是指可以被运行，具体有没有运行要看底层操作系统的资源调度。

  ### 阻塞（BLOCKED）

  请求获取 monitor lock 从而进入 synchronized 函数或者代码块，但是其它线程已经占用了该 monitor lock，所以出于阻塞状态。要结束该状态进入从而 RUNABLE 需要其他线程释放 monitor lock。

  ### 无限期等待（WAITING）

  等待其它线程显式地唤醒。

  阻塞和等待的区别在于，阻塞是被动的，它是在等待获取 monitor lock。而等待是主动的，通过调用 Object.wait() 等方法进入。

  | 进入方法                                   | 退出方法                             |
  | ------------------------------------------ | ------------------------------------ |
  | 没有设置 Timeout 参数的 Object.wait() 方法 | Object.notify() / Object.notifyAll() |
  | 没有设置 Timeout 参数的 Thread.join() 方法 | 被调用的线程执行完毕                 |
  | LockSupport.park() 方法                    | LockSupport.unpark(Thread)           |

  ### 限期等待（TIMED_WAITING）

  无需等待其它线程显式地唤醒，在一定时间之后会被系统自动唤醒。

  | 进入方法                                 | 退出方法                                        |
  | ---------------------------------------- | ----------------------------------------------- |
  | Thread.sleep() 方法                      | 时间结束                                        |
  | 设置了 Timeout 参数的 Object.wait() 方法 | 时间结束 / Object.notify() / Object.notifyAll() |
  | 设置了 Timeout 参数的 Thread.join() 方法 | 时间结束 / 被调用的线程执行完毕                 |
  | LockSupport.parkNanos() 方法             | LockSupport.unpark(Thread)                      |
  | LockSupport.parkUntil() 方法             | LockSupport.unpark(Thread)                      |

  调用 Thread.sleep() 方法使线程进入限期等待状态时，常常用“使一个线程睡眠”进行描述。调用 Object.wait() 方法使线程进入限期等待或者无限期等待时，常常用“挂起一个线程”进行描述。睡眠和挂起是用来描述行为，而阻塞和等待用来描述状态。

  ### 死亡（TERMINATED）

  可以是线程结束任务之后自己结束，或者产生了异常而结束。



* 参考材料
  * [Java线程的6种状态及切换(透彻讲解)](https://blog.csdn.net/pange1991/article/details/53860651)



### [J.U.C - AQS](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#七juc---aqs)

* 知识点
  - **CountDownLatch**
  
    用来控制一个或者多个线程等待多个线程。
  
    维护了一个计数器 cnt，每次调用 countDown() 方法会让计数器的值减 1，减到 0 的时候，那些因为调用 await() 方法而在等待的线程就会被唤醒。
  
    [![img](https://camo.githubusercontent.com/ceda39fe72e6d9797cbed436eb09cc974190b17a0b6c60266e235314ce5663b8/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f62613037383239312d373931652d343337382d623664312d6563653736633266306231342e706e67)](https://camo.githubusercontent.com/ceda39fe72e6d9797cbed436eb09cc974190b17a0b6c60266e235314ce5663b8/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f62613037383239312d373931652d343337382d623664312d6563653736633266306231342e706e67)
  
    
  
    ```java
    public class CountdownLatchExample {
    
        public static void main(String[] args) throws InterruptedException {
            final int totalThread = 10;
            CountDownLatch countDownLatch = new CountDownLatch(totalThread);
            ExecutorService executorService = Executors.newCachedThreadPool();
            for (int i = 0; i < totalThread; i++) {
                executorService.execute(() -> {
                    System.out.print("run..");
                    countDownLatch.countDown();
                });
            }
            countDownLatch.await();
            System.out.println("end");
            executorService.shutdown();
        }
    }
    
    run..run..run..run..run..run..run..run..run..run..end
    ```
  
    
  
  - **CyclicBarrier**
  
    用来控制多个线程互相等待，只有当多个线程都到达时，这些线程才会继续执行。
  
    和 CountdownLatch 相似，都是通过维护计数器来实现的。线程执行 await() 方法之后计数器会减 1，并进行等待，直到计数器为 0，所有调用 await() 方法而在等待的线程才能继续执行。
  
    CyclicBarrier 和 CountdownLatch 的一个区别是，CyclicBarrier 的计数器通过调用 reset() 方法可以循环使用，所以它才叫做循环屏障。
  
    CyclicBarrier 有两个构造函数，其中 parties 指示计数器的初始值，barrierAction 在所有线程都到达屏障的时候会执行一次。
  
    ```
    public CyclicBarrier(int parties, Runnable barrierAction) {
        if (parties <= 0) throw new IllegalArgumentException();
        this.parties = parties;
        this.count = parties;
        this.barrierCommand = barrierAction;
    }
    
    public CyclicBarrier(int parties) {
        this(parties, null);
    }
    ```
  
    ![img](https://camo.githubusercontent.com/347eb2206a468d116abe31c7aa1b55c503e186439043c75e6f3af79fffe1c1cd/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f66373161663636622d306435342d343339392d613434622d6634376235383332313938342e706e67)
  
    
  
    ```
    public class CyclicBarrierExample {
    
        public static void main(String[] args) {
            final int totalThread = 10;
            CyclicBarrier cyclicBarrier = new CyclicBarrier(totalThread);
            ExecutorService executorService = Executors.newCachedThreadPool();
            for (int i = 0; i < totalThread; i++) {
                executorService.execute(() -> {
                    System.out.print("before..");
                    try {
                        cyclicBarrier.await();
                    } catch (InterruptedException | BrokenBarrierException e) {
                        e.printStackTrace();
                    }
                    System.out.print("after..");
                });
            }
            executorService.shutdown();
        }
    }
    
    
    before..before..before..before..before..before..before..before..before..before..after..after..after..after..after..after..after..after..after..after..
    ```
  
    
  
  - **Semaphore**
  
    Semaphore 类似于操作系统中的信号量，可以控制对互斥资源的访问线程数。
  
    以下代码模拟了对某个服务的并发请求，每次只能有 3 个客户端同时访问，请求总数为 10。
  
    ```java
    public class SemaphoreExample {
    
        public static void main(String[] args) {
            final int clientCount = 3;
            final int totalRequestCount = 10;
            Semaphore semaphore = new Semaphore(clientCount);
            ExecutorService executorService = Executors.newCachedThreadPool();
            for (int i = 0; i < totalRequestCount; i++) {
                executorService.execute(()->{
                    try {
                        semaphore.acquire();
                        System.out.print(semaphore.availablePermits() + " ");
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        semaphore.release();
                    }
                });
            }
            executorService.shutdown();
        }
    }
    ```
  
    
  
    ```
    2 1 2 2 2 2 2 1 2 2
    ```



### [J.U.C - 其它组件](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#八juc---其它组件)

* 知识点
  - **FutureTask**

    在介绍 Callable 时我们知道它可以有返回值，返回值通过 Future<V> 进行封装。FutureTask 实现了 RunnableFuture 接口，该接口继承自 Runnable 和 Future<V> 接口，这使得 FutureTask 既可以当做一个任务执行，也可以有返回值。

    ```
    public class FutureTask<V> implements RunnableFuture<V>
    public interface RunnableFuture<V> extends Runnable, Future<V>
    ```

    FutureTask 可用于异步获取执行结果或取消执行任务的场景。当一个计算任务需要执行很长时间，那么就可以用 FutureTask 来封装这个任务，主线程在完成自己的任务之后再去获取结果。

    ```java
    public class FutureTaskExample {
    
        public static void main(String[] args) throws ExecutionException, InterruptedException {
            FutureTask<Integer> futureTask = new FutureTask<Integer>(new Callable<Integer>() {
                @Override
                public Integer call() throws Exception {
                    int result = 0;
                    for (int i = 0; i < 100; i++) {
                        Thread.sleep(10);
                        result += i;
                    }
                    return result;
                }
            });
    
            Thread computeThread = new Thread(futureTask);
            computeThread.start();
    
            Thread otherThread = new Thread(() -> {
                System.out.println("other task is running...");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
            otherThread.start();
            System.out.println(futureTask.get());
        }
    }
    ```

    ```
    other task is running...
    4950
    ```

    

  - **BlockingQueue**

    java.util.concurrent.BlockingQueue 接口有以下阻塞队列的实现：

    - **FIFO 队列** ：LinkedBlockingQueue、ArrayBlockingQueue（固定长度）
    - **优先级队列** ：PriorityBlockingQueue

    提供了阻塞的 take() 和 put() 方法：如果队列为空 take() 将阻塞，直到队列中有内容；如果队列为满 put() 将阻塞，直到队列有空闲位置。

    **使用 BlockingQueue 实现生产者消费者问题**

    ```java
    public class ProducerConsumer {
    
        private static BlockingQueue<String> queue = new ArrayBlockingQueue<>(5);
    
        private static class Producer extends Thread {
            @Override
            public void run() {
                try {
                    queue.put("product");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.print("produce..");
            }
        }
    
        private static class Consumer extends Thread {
    
            @Override
            public void run() {
                try {
                    String product = queue.take();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.print("consume..");
            }
        }
    }
    ```

    ```java
    public static void main(String[] args) {
        for (int i = 0; i < 2; i++) {
            Producer producer = new Producer();
            producer.start();
        }
        for (int i = 0; i < 5; i++) {
            Consumer consumer = new Consumer();
            consumer.start();
        }
        for (int i = 0; i < 3; i++) {
            Producer producer = new Producer();
            producer.start();
        }
    }
    
    produce..produce..consume..consume..produce..consume..produce..consume..produce..consume..
    ```

    

  - **ForkJoin**

    主要用于并行计算中，和 MapReduce 原理类似，都是把大的计算任务拆分成多个小任务并行计算。

    ```
    public class ForkJoinExample extends RecursiveTask<Integer> {
    
        private final int threshold = 5;
        private int first;
        private int last;
    
        public ForkJoinExample(int first, int last) {
            this.first = first;
            this.last = last;
        }
    
        @Override
        protected Integer compute() {
            int result = 0;
            if (last - first <= threshold) {
                // 任务足够小则直接计算
                for (int i = first; i <= last; i++) {
                    result += i;
                }
            } else {
                // 拆分成小任务
                int middle = first + (last - first) / 2;
                ForkJoinExample leftTask = new ForkJoinExample(first, middle);
                ForkJoinExample rightTask = new ForkJoinExample(middle + 1, last);
                leftTask.fork();
                rightTask.fork();
                result = leftTask.join() + rightTask.join();
            }
            return result;
        }
    }
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ForkJoinExample example = new ForkJoinExample(1, 10000);
        ForkJoinPool forkJoinPool = new ForkJoinPool();
        Future result = forkJoinPool.submit(example);
        System.out.println(result.get());
    }
    ```

    ForkJoin 使用 ForkJoinPool 来启动，它是一个特殊的线程池，线程数量取决于 CPU 核数。

    ```
    public class ForkJoinPool extends AbstractExecutorService
    ```

    ForkJoinPool 实现了工作窃取算法来提高 CPU 的利用率。每个线程都维护了一个双端队列，用来存储需要执行的任务。工作窃取算法允许空闲的线程从其它线程的双端队列中窃取一个任务来执行。窃取的任务必须是最晚的任务，避免和队列所属线程发生竞争。例如下图中，Thread2 从 Thread1 的队列中拿出最晚的 Task1 任务，Thread1 会拿出 Task2 来执行，这样就避免发生竞争。但是如果队列中只有一个任务时还是会发生竞争。

    ![img](https://camo.githubusercontent.com/2dea3a33076fa2b7b8b55127ed7ff8b0e59f8b93a1156c3213af9d8d1f179384/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f65343266313838662d663461392d346536662d383866632d3435663436383230373266622e706e67)

* 参考资料
  
  * [Java Fork/Join 框架](https://www.cnblogs.com/cjsblog/p/9078341.html)



### [Java 内存模型](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#十java-内存模型)

* 知识点

  Java 内存模型试图屏蔽各种硬件和操作系统的内存访问差异，以实现让 Java 程序在各种平台下都能达到一致的内存访问效果。
  
  - **主内存与工作内存**
  
    处理器上的寄存器的读写的速度比内存快几个数量级，为了解决这种速度矛盾，在它们之间加入了高速缓存。
  
    加入高速缓存带来了一个新的问题：缓存一致性。如果多个缓存共享同一块主内存区域，那么多个缓存的数据可能会不一致，需要一些协议来解决这个问题。
  
    ![img](https://camo.githubusercontent.com/7df4b55b854db759d86b9f23d514ccdb8b8e28c55316fdc50048e8b9013b622e/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f39343263613064322d396435632d343561342d383963622d3566643839623631393133662e706e67)
  
    
  
    所有的变量都存储在主内存中，每个线程还有自己的工作内存，工作内存存储在高速缓存或者寄存器中，保存了该线程使用的变量的主内存副本拷贝。
  
    线程只能直接操作工作内存中的变量，不同线程之间的变量值传递需要通过主内存来完成。
  
    [![img](https://camo.githubusercontent.com/0306fdc0e9fd98a72dbd96e84fba9ec578f3e81814bc42ee96ed4c5854e11cac/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f31353835313535352d356162632d343937642d616433342d6566656431306634336136622e706e67)](https://camo.githubusercontent.com/0306fdc0e9fd98a72dbd96e84fba9ec578f3e81814bc42ee96ed4c5854e11cac/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f31353835313535352d356162632d343937642d616433342d6566656431306634336136622e706e67)
  
  - **内存间交互操作**
  
    Java 内存模型定义了 8 个操作来完成主内存和工作内存的交互操作。
  
    ![img](https://camo.githubusercontent.com/eef382ee00242697145ffb3eec7bef5f7babd95a0500e4a43096854ea67ca9ae/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f38623765626261642d393630342d343337352d383465332d6634313230393964313730632e706e67)
  
    
  
    - read：把一个变量的值从主内存传输到工作内存中
    - load：在 read 之后执行，把 read 得到的值放入工作内存的变量副本中
    - use：把工作内存中一个变量的值传递给执行引擎
    - assign：把一个从执行引擎接收到的值赋给工作内存的变量
    - store：把工作内存的一个变量的值传送到主内存中
    - write：在 store 之后执行，把 store 得到的值放入主内存的变量中
    - lock：作用于主内存的变量
    - unlock
  
    
  
  - **内存模型三大特性**
  
    #### 1. 原子性
  
    Java 内存模型保证了 read、load、use、assign、store、write、lock 和 unlock 操作具有原子性，例如对一个 int 类型的变量执行 assign 赋值操作，这个操作就是原子性的。但是 Java 内存模型允许虚拟机将没有被 volatile 修饰的 64 位数据（long，double）的读写操作划分为两次 32 位的操作来进行，即 load、store、read 和 write 操作可以不具备原子性。
  
    有一个错误认识就是，int 等原子性的类型在多线程环境中不会出现线程安全问题。前面的线程不安全示例代码中，cnt 属于 int 类型变量，1000 个线程对它进行自增操作之后，得到的值为 997 而不是 1000。
  
    为了方便讨论，将内存间的交互操作简化为 3 个：load、assign、store。
  
    下图演示了两个线程同时对 cnt 进行操作，load、assign、store 这一系列操作整体上看不具备原子性，那么在 T1 修改 cnt 并且还没有将修改后的值写入主内存，T2 依然可以读入旧值。可以看出，这两个线程虽然执行了两次自增运算，但是主内存中 cnt 的值最后为 1 而不是 2。因此对 int 类型读写操作满足原子性只是说明 load、assign、store 这些单个操作具备原子性。
  
    ![img](https://camo.githubusercontent.com/f8ea9713621160394929ed10092d22ca9a72940f15c23722c14cb409465dfee9/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f32373937613630392d363864622d346437622d383730312d3431616339613334623134662e6a7067)
  
    AtomicInteger 能保证多个线程修改的原子性。
  
    ![img](https://camo.githubusercontent.com/6a12b545df7c5274334649b2a703d47c57afa4b4fd57eab2510dccaa3aeefbcc/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f64643536333033372d666361612d346264382d383362362d6233396439336131326337372e6a7067)
  
    
  
    使用 AtomicInteger 重写之前线程不安全的代码之后得到以下线程安全实现：
  
    ```java
    public class AtomicExample {
        private AtomicInteger cnt = new AtomicInteger();
    
        public void add() {
            cnt.incrementAndGet();
        }
    
        public int get() {
            return cnt.get();
        }
    }
    ```
  
    ```java
    public static void main(String[] args) throws InterruptedException {
        final int threadSize = 1000;
        AtomicExample example = new AtomicExample(); // 只修改这条语句
        final CountDownLatch countDownLatch = new CountDownLatch(threadSize);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < threadSize; i++) {
            executorService.execute(() -> {
                example.add();
                countDownLatch.countDown();
            });
        }
        countDownLatch.await();
        executorService.shutdown();
        System.out.println(example.get());
    }
    
    1000
    ```
  
    
  
    除了使用原子类之外，也可以使用 synchronized 互斥锁来保证操作的原子性。它对应的内存间交互操作为：lock 和 unlock，在虚拟机实现上对应的字节码指令为 monitorenter 和 monitorexit。
  
    ```java
    public class AtomicSynchronizedExample {
        private int cnt = 0;
    
        public synchronized void add() {
            cnt++;
        }
    
        public synchronized int get() {
            return cnt;
        }
    }
    
    ```
  
    
  
    ```java
    public static void main(String[] args) throws InterruptedException {
        final int threadSize = 1000;
        AtomicSynchronizedExample example = new AtomicSynchronizedExample();
        final CountDownLatch countDownLatch = new CountDownLatch(threadSize);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < threadSize; i++) {
            executorService.execute(() -> {
                example.add();
                countDownLatch.countDown();
            });
        }
        countDownLatch.await();
        executorService.shutdown();
        System.out.println(example.get());
    }
    
    1000
    ```
  
    
  
    #### 2. 可见性
  
    可见性指当一个线程修改了共享变量的值，其它线程能够立即得知这个修改。Java 内存模型是通过在变量修改后将新值同步回主内存，在变量读取前从主内存刷新变量值来实现可见性的。
  
    主要有三种实现可见性的方式：
  
    - volatile
    - synchronized，对一个变量执行 unlock 操作之前，必须把变量值同步回主内存。
    - final，被 final 关键字修饰的字段在构造器中一旦初始化完成，并且没有发生 this 逃逸（其它线程通过 this 引用访问到初始化了一半的对象），那么其它线程就能看见 final 字段的值。
  
    对前面的线程不安全示例中的 cnt 变量使用 volatile 修饰，不能解决线程不安全问题，因为 volatile 并不能保证操作的原子性。
  
    #### 3. 有序性
  
    有序性是指：在本线程内观察，所有操作都是有序的。在一个线程观察另一个线程，所有操作都是无序的，无序是因为发生了指令重排序。在 Java 内存模型中，允许编译器和处理器对指令进行重排序，重排序过程不会影响到单线程程序的执行，却会影响到多线程并发执行的正确性。
  
    volatile 关键字通过添加内存屏障的方式来禁止指令重排，即重排序时不能把后面的指令放到内存屏障之前。
  
    也可以通过 synchronized 来保证有序性，它保证每个时刻只有一个线程执行同步代码，相当于是让线程顺序执行同步代码。
  
  
  
  - **先行发生原则**
  
    上面提到了可以用 volatile 和 synchronized 来保证有序性。除此之外，JVM 还规定了先行发生原则，让一个操作无需控制就能先于另一个操作完成。
  
    #### 1. 单一线程原则
  
    > Single Thread rule
  
    在一个线程内，在程序前面的操作先行发生于后面的操作。
  
    [![img](https://camo.githubusercontent.com/e127e9f81ad74b7b70caab174b90b58ba21b2580d7ebab162e2624d39b8910a4/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f38373462336666372d376335632d346537612d623861622d6138326133653033386432302e706e67)](https://camo.githubusercontent.com/e127e9f81ad74b7b70caab174b90b58ba21b2580d7ebab162e2624d39b8910a4/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f38373462336666372d376335632d346537612d623861622d6138326133653033386432302e706e67)
  
    
  
    #### 2. 管程锁定规则
  
    > Monitor Lock Rule
  
    一个 unlock 操作先行发生于后面对同一个锁的 lock 操作。
  
    [![img](https://camo.githubusercontent.com/f1b9eff827102f0f43ac288fdb53a92c61ce9b61cb00e723bfe3a4b4b2f2c2bf/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f38393936613533372d376334612d346563382d613362372d3765663137393865616532362e706e67)](https://camo.githubusercontent.com/f1b9eff827102f0f43ac288fdb53a92c61ce9b61cb00e723bfe3a4b4b2f2c2bf/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f38393936613533372d376334612d346563382d613362372d3765663137393865616532362e706e67)
  
    
  
    #### 3. volatile 变量规则
  
    > Volatile Variable Rule
  
    对一个 volatile 变量的写操作先行发生于后面对这个变量的读操作。
  
    [![img](https://camo.githubusercontent.com/d07656ed0d1a5766b0f17d9caaf5b4f2c945dfb316cd1150a7e117313029ad9b/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f39343266333363392d386164392d343938372d383336662d3030376465346332316465302e706e67)](https://camo.githubusercontent.com/d07656ed0d1a5766b0f17d9caaf5b4f2c945dfb316cd1150a7e117313029ad9b/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f39343266333363392d386164392d343938372d383336662d3030376465346332316465302e706e67)
  
    
  
    #### 4. 线程启动规则
  
    > Thread Start Rule
  
    Thread 对象的 start() 方法调用先行发生于此线程的每一个动作。
  
    [![img](https://camo.githubusercontent.com/b4a2ee9c41bda349f8b45a93f9c36a9d20639ed50deee7a5a289856b65c284c0/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f36323730633231362d376563302d346462372d393464652d3030303362636533376364322e706e67)](https://camo.githubusercontent.com/b4a2ee9c41bda349f8b45a93f9c36a9d20639ed50deee7a5a289856b65c284c0/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f36323730633231362d376563302d346462372d393464652d3030303362636533376364322e706e67)
  
    
  
    #### 5. 线程加入规则
  
    > Thread Join Rule
  
    Thread 对象的结束先行发生于 join() 方法返回。
  
    ![img](https://camo.githubusercontent.com/43d37a01276be3333f44e6fdc91aafce84a29d53d5c0d7146854df71151869f5/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f32333366386438392d333164372d343133662d396330322d3034326631396334366261312e706e67)
  
    
  
    #### 6. 线程中断规则
  
    > Thread Interruption Rule
  
    对线程 interrupt() 方法的调用先行发生于被中断线程的代码检测到中断事件的发生，可以通过 interrupted() 方法检测到是否有中断发生。
  
    #### 7. 对象终结规则
  
    > Finalizer Rule
  
    一个对象的初始化完成（构造函数执行结束）先行发生于它的 finalize() 方法的开始。
  
    #### 8. 传递性
  
    > Transitivity
  
    如果操作 A 先行发生于操作 B，操作 B 先行发生于操作 C，那么操作 A 先行发生于操作 C。
    
    
    
  - **Volatile**
  
  
  
* 参考资料

  * [彻底理解volatile](https://juejin.cn/post/6844903601064640525)
  * [volatile关键字的作用、原理](https://juejin.cn/post/6844903502418804743)
  * [面试官没想到一个Volatile，我都能跟他扯半小时](https://juejin.cn/post/6844904149536997384)



### [线程安全](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#十一线程安全)

* 知识点

  多个线程不管以何种方式访问某个类，并且在主调代码中不需要进行同步，都能表现正确的行为。

  线程安全有以下几种实现方式：**不可变、互斥同步、非阻塞同步、无同步方案**。

  - **不可变**

    不可变（Immutable）的对象一定是线程安全的，不需要再采取任何的线程安全保障措施。只要一个不可变的对象被正确地构建出来，永远也不会看到它在多个线程之中处于不一致的状态。多线程环境下，应当尽量使对象成为不可变，来满足线程安全。

    不可变的类型：

    - final 关键字修饰的基本数据类型
    - String
    - 枚举类型
    - Number 部分子类，如 Long 和 Double 等数值包装类型，BigInteger 和 BigDecimal 等大数据类型。但同为 Number 的原子类 AtomicInteger 和 AtomicLong 则是可变的。

    对于集合类型，可以使用 Collections.unmodifiableXXX() 方法来获取一个不可变的集合。

    ```
    public class ImmutableExample {
        public static void main(String[] args) {
            Map<String, Integer> map = new HashMap<>();
            Map<String, Integer> unmodifiableMap = Collections.unmodifiableMap(map);
            unmodifiableMap.put("a", 1);
        }
    }
    Exception in thread "main" java.lang.UnsupportedOperationException
        at java.util.Collections$UnmodifiableMap.put(Collections.java:1457)
        at ImmutableExample.main(ImmutableExample.java:9)
    ```

    Collections.unmodifiableXXX() 先对原始的集合进行拷贝，需要对集合进行修改的方法都直接抛出异常。

    ```
    public V put(K key, V value) {
        throw new UnsupportedOperationException();
    }
    ```

    

  - **互斥同步**

    synchronized 和 ReentrantLock。
  
    
  
  - **非阻塞同步**
  
    互斥同步最主要的问题就是线程阻塞和唤醒所带来的性能问题，因此这种同步也称为阻塞同步。
  
    互斥同步属于一种悲观的并发策略，总是认为只要不去做正确的同步措施，那就肯定会出现问题。无论共享数据是否真的会出现竞争，它都要进行加锁（这里讨论的是概念模型，实际上虚拟机会优化掉很大一部分不必要的加锁）、用户态核心态转换、维护锁计数器和检查是否有被阻塞的线程需要唤醒等操作。
  
    随着硬件指令集的发展，我们可以使用基于冲突检测的乐观并发策略：先进行操作，如果没有其它线程争用共享数据，那操作就成功了，否则采取补偿措施（不断地重试，直到成功为止）。这种乐观的并发策略的许多实现都不需要将线程阻塞，因此这种同步操作称为非阻塞同步。
  
    #### 1. CAS
  
    乐观锁需要操作和冲突检测这两个步骤具备原子性，这里就不能再使用互斥同步来保证了，只能靠硬件来完成。硬件支持的原子性操作最典型的是：比较并交换（Compare-and-Swap，CAS）。CAS 指令需要有 3 个操作数，分别是内存地址 V、旧的预期值 A 和新值 B。当执行操作时，只有当 V 的值等于 A，才将 V 的值更新为 B。
  
    
  
    #### 2. AtomicInteger
  
    J.U.C 包里面的整数原子类 AtomicInteger 的方法调用了 Unsafe 类的 CAS 操作。
  
    以下代码使用了 AtomicInteger 执行了自增的操作。
  
    ```
    private AtomicInteger cnt = new AtomicInteger();
    
    public void add() {
        cnt.incrementAndGet();
    }
    ```
  
    以下代码是 incrementAndGet() 的源码，它调用了 Unsafe 的 getAndAddInt() 。
  
    ```
    public final int incrementAndGet() {
        return unsafe.getAndAddInt(this, valueOffset, 1) + 1;
    }
    ```
  
    以下代码是 getAndAddInt() 源码，var1 指示对象内存地址，var2 指示该字段相对对象内存地址的偏移，var4 指示操作需要加的数值，这里为 1。通过 getIntVolatile(var1, var2) 得到旧的预期值，通过调用 compareAndSwapInt() 来进行 CAS 比较，如果该字段内存地址中的值等于 var5，那么就更新内存地址为 var1+var2 的变量为 var5+var4。
  
    可以看到 getAndAddInt() 在一个循环中进行，发生冲突的做法是不断的进行重试。
  
    ```java
    public final int getAndAddInt(Object var1, long var2, int var4) {
        int var5;
        do {
            var5 = this.getIntVolatile(var1, var2);
        } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));
    
        return var5;
    }
    ```
  
    
  
    #### 3. ABA
  
    如果一个变量初次读取的时候是 A 值，它的值被改成了 B，后来又被改回为 A，那 CAS 操作就会误认为它从来没有被改变过。
  
    J.U.C 包提供了一个带有标记的原子引用类 AtomicStampedReference 来解决这个问题，它可以通过控制变量值的版本来保证 CAS 的正确性。大部分情况下 ABA 问题不会影响程序并发的正确性，如果需要解决 ABA 问题，改用传统的互斥同步可能会比原子类更高效。
  
    
  
  - **无同步方案**
  
    要保证线程安全，并不是一定就要进行同步。如果一个方法本来就不涉及共享数据，那它自然就无须任何同步措施去保证正确性。
  
    #### 1. 栈封闭
  
    多个线程访问同一个方法的局部变量时，不会出现线程安全问题，因为局部变量存储在虚拟机栈中，属于线程私有的。
  
    ```
    public class StackClosedExample {
        public void add100() {
            int cnt = 0;
            for (int i = 0; i < 100; i++) {
                cnt++;
            }
            System.out.println(cnt);
        }
    }
    public static void main(String[] args) {
        StackClosedExample example = new StackClosedExample();
        ExecutorService executorService = Executors.newCachedThreadPool();
        executorService.execute(() -> example.add100());
        executorService.execute(() -> example.add100());
        executorService.shutdown();
    }
    100
    100
    ```
  
    #### 2. 线程本地存储（Thread Local Storage）
  
    如果一段代码中所需要的数据必须与其他代码共享，那就看看这些共享数据的代码是否能保证在同一个线程中执行。如果能保证，我们就可以把共享数据的可见范围限制在同一个线程之内，这样，无须同步也能保证线程之间不出现数据争用的问题。
  
    符合这种特点的应用并不少见，大部分使用消费队列的架构模式（如“生产者-消费者”模式）都会将产品的消费过程尽量在一个线程中消费完。其中最重要的一个应用实例就是经典 Web 交互模型中的“一个请求对应一个服务器线程”（Thread-per-Request）的处理方式，这种处理方式的广泛应用使得很多 Web 服务端应用都可以使用线程本地存储来解决线程安全问题。
  
    可以使用 java.lang.ThreadLocal 类来实现线程本地存储功能。
  
    对于以下代码，thread1 中设置 threadLocal 为 1，而 thread2 设置 threadLocal 为 2。过了一段时间之后，thread1 读取 threadLocal 依然是 1，不受 thread2 的影响。
  
    ```
    public class ThreadLocalExample {
        public static void main(String[] args) {
            ThreadLocal threadLocal = new ThreadLocal();
            Thread thread1 = new Thread(() -> {
                threadLocal.set(1);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(threadLocal.get());
                threadLocal.remove();
            });
            Thread thread2 = new Thread(() -> {
                threadLocal.set(2);
                threadLocal.remove();
            });
            thread1.start();
            thread2.start();
        }
    }
    1
    ```
  
    为了理解 ThreadLocal，先看以下代码：
  
    ```
    public class ThreadLocalExample1 {
        public static void main(String[] args) {
            ThreadLocal threadLocal1 = new ThreadLocal();
            ThreadLocal threadLocal2 = new ThreadLocal();
            Thread thread1 = new Thread(() -> {
                threadLocal1.set(1);
                threadLocal2.set(1);
            });
            Thread thread2 = new Thread(() -> {
                threadLocal1.set(2);
                threadLocal2.set(2);
            });
            thread1.start();
            thread2.start();
        }
    }
    ```
  
    它所对应的底层结构图为：
  
    [![img](https://camo.githubusercontent.com/4b220b71849a86d4fecc7f3894f081d40ecf11d2276e24d1d0d750c11f179d0f/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f36373832363734632d316266652d343837392d616633392d6539643732326139356433392e706e67)](https://camo.githubusercontent.com/4b220b71849a86d4fecc7f3894f081d40ecf11d2276e24d1d0d750c11f179d0f/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f36373832363734632d316266652d343837392d616633392d6539643732326139356433392e706e67)
  
    
  
    每个 Thread 都有一个 ThreadLocal.ThreadLocalMap 对象。
  
    ```
    /* ThreadLocal values pertaining to this thread. This map is maintained
     * by the ThreadLocal class. */
    ThreadLocal.ThreadLocalMap threadLocals = null;
    ```
  
    当调用一个 ThreadLocal 的 set(T value) 方法时，先得到当前线程的 ThreadLocalMap 对象，然后将 ThreadLocal->value 键值对插入到该 Map 中。
  
    ```
    public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
    }
    ```
  
    get() 方法类似。
  
    ```
    public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue();
    }
    ```
  
    ThreadLocal 从理论上讲并不是用来解决多线程并发问题的，因为根本不存在多线程竞争。
  
    在一些场景 (尤其是使用线程池) 下，由于 ThreadLocal.ThreadLocalMap 的底层数据结构导致 ThreadLocal 有内存泄漏的情况，应该尽可能在每次使用 ThreadLocal 后手动调用 remove()，以避免出现 ThreadLocal 经典的内存泄漏甚至是造成自身业务混乱的风险。
  
    #### 3. 可重入代码（Reentrant Code）
  
    这种代码也叫做纯代码（Pure Code），可以在代码执行的任何时刻中断它，转而去执行另外一段代码（包括递归调用它本身），而在控制权返回后，原来的程序不会出现任何错误。
  
    可重入代码有一些共同的特征，例如不依赖存储在堆上的数据和公用的系统资源、用到的状态量都由参数中传入、不调用非可重入的方法等。



### 锁类型

* 知识点

  * 阅读 [不可不说的Java“锁”事](https://tech.meituan.com/2018/11/15/java-lock.html)  即可

  ![img](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2018b/7f749fc8.png)

* 参考资料

  * [不可不说的Java“锁”事](https://tech.meituan.com/2018/11/15/java-lock.html)
  * [《蹲坑也能进大厂》多线程系列 - 悲观锁、乐观锁、可重入/不可重入锁 详解](https://juejin.cn/post/6978131721845899295)



### 锁优化

* 知识点

  这里的锁优化主要是指 JVM 对 synchronized 的优化。

  

  - **自旋锁**

    互斥同步进入阻塞状态的开销都很大，应该尽量避免。在许多应用中，共享数据的锁定状态只会持续很短的一段时间。自旋锁的思想是让一个线程在请求一个共享数据的锁时执行忙循环（自旋）一段时间，如果在这段时间内能获得锁，就可以避免进入阻塞状态。

    自旋锁虽然能避免进入阻塞状态从而减少开销，但是它需要进行忙循环操作占用 CPU 时间，它只适用于共享数据的锁定状态很短的场景。

    在 JDK 1.6 中引入了自适应的自旋锁。自适应意味着自旋的次数不再固定了，而是由前一次在同一个锁上的自旋次数及锁的拥有者的状态来决定。

    

  - **锁消除**

    锁消除是指对于被检测出不可能存在竞争的共享数据的锁进行消除。

    锁消除主要是通过逃逸分析来支持，如果堆上的共享数据不可能逃逸出去被其它线程访问到，那么就可以把它们当成私有数据对待，也就可以将它们的锁进行消除。

    对于一些看起来没有加锁的代码，其实隐式的加了很多锁。例如下面的字符串拼接代码就隐式加了锁：

    ```
    public static String concatString(String s1, String s2, String s3) {
        return s1 + s2 + s3;
    }
    ```

    String 是一个不可变的类，编译器会对 String 的拼接自动优化。在 JDK 1.5 之前，会转化为 StringBuffer 对象的连续 append() 操作：

    ```
    public static String concatString(String s1, String s2, String s3) {
        StringBuffer sb = new StringBuffer();
        sb.append(s1);
        sb.append(s2);
        sb.append(s3);
        return sb.toString();
    }
    ```

    每个 append() 方法中都有一个同步块。虚拟机观察变量 sb，很快就会发现它的动态作用域被限制在 concatString() 方法内部。也就是说，sb 的所有引用永远不会逃逸到 concatString() 方法之外，其他线程无法访问到它，因此可以进行消除。

    

  - **锁粗化**

    如果一系列的连续操作都对同一个对象反复加锁和解锁，频繁的加锁操作就会导致性能损耗。

    上一节的示例代码中连续的 append() 方法就属于这类情况。如果虚拟机探测到由这样的一串零碎的操作都对同一个对象加锁，将会把加锁的范围扩展（粗化）到整个操作序列的外部。对于上一节的示例代码就是扩展到第一个 append() 操作之前直至最后一个 append() 操作之后，这样只需要加锁一次就可以了。

    

  - **轻量级锁**

    JDK 1.6 引入了偏向锁和轻量级锁，从而让锁拥有了四个状态：无锁状态（unlocked）、偏向锁状态（biasble）、轻量级锁状态（lightweight locked）和重量级锁状态（inflated）。

    以下是 HotSpot 虚拟机对象头的内存布局，这些数据被称为 Mark Word。其中 tag bits 对应了五个状态，这些状态在右侧的 state 表格中给出。除了 marked for gc 状态，其它四个状态已经在前面介绍过了。

    ![img](https://camo.githubusercontent.com/244b36284e8af2b9991999bc998ce6ec787bc1d606d02fb3c480c078830af57e/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f62623661343962652d303066322d346632372d613063652d3465643736346263363035632e706e67)

    

    下图左侧是一个线程的虚拟机栈，其中有一部分称为 Lock Record 的区域，这是在轻量级锁运行过程创建的，用于存放锁对象的 Mark Word。而右侧就是一个锁对象，包含了 Mark Word 和其它信息。

    ![img](https://camo.githubusercontent.com/0d43ae0c3a70eac2641a88726e815ec1f49e04ef567767f23f5478fc26b971bd/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f30353165343336632d306534362d346335392d386636372d3532643839643635363138322e706e67)

    

    轻量级锁是相对于传统的重量级锁而言，它使用 CAS 操作来避免重量级锁使用互斥量的开销。对于绝大部分的锁，在整个同步周期内都是不存在竞争的，因此也就不需要都使用互斥量进行同步，可以先采用 CAS 操作进行同步，如果 CAS 失败了再改用互斥量进行同步。

    当尝试获取一个锁对象时，如果锁对象标记为 0 01，说明锁对象的锁未锁定（unlocked）状态。此时虚拟机在当前线程的虚拟机栈中创建 Lock Record，然后使用 CAS 操作将对象的 Mark Word 更新为 Lock Record 指针。如果 CAS 操作成功了，那么线程就获取了该对象上的锁，并且对象的 Mark Word 的锁标记变为 00，表示该对象处于轻量级锁状态。

    ![img](https://camo.githubusercontent.com/910c1d1e3463d96ca4875a2d4db5156ed7533136d5eca07e1190258964f161ea/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f62616161363831662d376335322d343139382d613561652d3330336239333836636634372e706e67)

    

    如果 CAS 操作失败了，虚拟机首先会检查对象的 Mark Word 是否指向当前线程的虚拟机栈，如果是的话说明当前线程已经拥有了这个锁对象，那就可以直接进入同步块继续执行，否则说明这个锁对象已经被其他线程线程抢占了。如果有两条以上的线程争用同一个锁，那轻量级锁就不再有效，要膨胀为重量级锁。

    

  - **偏向锁**

    偏向锁的思想是偏向于让第一个获取锁对象的线程，这个线程在之后获取该锁就不再需要进行同步操作，甚至连 CAS 操作也不再需要。

    当锁对象第一次被线程获得的时候，进入偏向状态，标记为 1 01。同时使用 CAS 操作将线程 ID 记录到 Mark Word 中，如果 CAS 操作成功，这个线程以后每次进入这个锁相关的同步块就不需要再进行任何同步操作。

    当有另外一个线程去尝试获取这个锁对象时，偏向状态就宣告结束，此时撤销偏向（Revoke Bias）后恢复到未锁定状态或者轻量级锁状态。

    ![img](https://camo.githubusercontent.com/95d0ad9a48474178e9ab5fcfc994d768a904d1e53fce6c144a58340a6f71dc22/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f33393063393133622d356633312d343434662d626264622d3262383862363838653763652e6a7067)

* 参考资料
  
  * [锁优化](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E5%B9%B6%E5%8F%91.md#%E5%8D%81%E4%BA%8C%E9%94%81%E4%BC%98%E5%8C%96)



### [多线程开发良好的实践](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java 并发.md#十三多线程开发良好的实践)

- 给线程起个有意义的名字，这样可以方便找 Bug。
- 缩小同步范围，从而减少锁争用。例如对于 synchronized，应该尽量使用同步块而不是同步方法。
- 多用同步工具少用 wait() 和 notify()。首先，CountDownLatch, CyclicBarrier, Semaphore 和 Exchanger 这些同步类简化了编码操作，而用 wait() 和 notify() 很难实现复杂控制流；其次，这些同步类是由最好的企业编写和维护，在后续的 JDK 中还会不断优化和完善。
- 使用 BlockingQueue 实现生产者消费者问题。
- 多用并发集合少用同步集合，例如应该使用 ConcurrentHashMap 而不是 Hashtable。
- 使用本地变量和不可变类来保证线程安全。
- 使用线程池而不是直接创建线程，这是因为创建线程代价很高，线程池可以有效地利用有限的线程来启动任务。



### 线程池

* 知识点
  - **线程池的使用**
  
    1. 使用线程池工具类Executors来快捷的创建线程池
  
       ```java
       // 实例化一个单线程的线程池
       ExecutorService singleExecutor = Executors.newSingleThreadExecutor();
       // 创建固定线程个数的线程池
       ExecutorService fixedExecutor = Executors.newFixedThreadPool(10);
       // 创建一个可重用固定线程数的线程池
       ExecutorService executorService2 = Executors.newCachedThreadPool();
       ```
  
       
  
    2. 通过ThreadPoolExecutor的构造方法来实例化出一个线程池。创建好线程池后直接调用execute方法或submit方法并传入一个Runnable参数即可将任务交给线程池执行，通过shutdown/shutdownNow方法可以关闭线程池。
  
       ```java
       // 实例化一个线程池
       ThreadPoolExecutor executor = new ThreadPoolExecutor(3, 10, 60,
               TimeUnit.SECONDS, new ArrayBlockingQueue<>(20));
       // 使用线程池执行一个任务        
       executor.execute(() -> {
           // Do something
       });
       // 关闭线程池,会阻止新任务提交，但不影响已提交的任务
       executor.shutdown();
       // 关闭线程池，阻止新任务提交，并且中断当前正在运行的线程
       executor.showdownNow();
       ```
  
    
  
  - **线程池的生命周期**
  
    线程池从诞生到死亡，中间会经历RUNNING、SHUTDOWN、STOP、TIDYING、TERMINATED五个生命周期状态。
  
    
  
    - RUNNING 表示线程池处于运行状态，能够接受新提交的任务且能对已添加的任务进行处理。RUNNING状态是线程池的初始化状态，线程池一旦被创建就处于RUNNING状态。
    - SHUTDOWN 线程处于关闭状态，不接受新任务，但可以处理已添加的任务。RUNNING状态的线程池调用shutdown后会进入SHUTDOWN状态。
    - STOP 线程池处于停止状态，不接收任务，不处理已添加的任务，且会中断正在执行任务的线程。RUNNING状态的线程池调用了shutdownNow后会进入STOP状态。
    - TIDYING 当所有任务已终止，且任务数量为0时，线程池会进入TIDYING。当线程池处于SHUTDOWN状态时，阻塞队列中的任务被执行完了，且线程池中没有正在执行的任务了，状态会由SHUTDOWN变为TIDYING。当线程处于STOP状态时，线程池中没有正在执行的任务时则会由STOP变为TIDYING。
    - TERMINATED 线程终止状态。处于TIDYING状态的线程执行terminated()后进入TERMINATED状态。
  
    
  
    根据上述线程池生命周期状态的描述，可以画出如下所示的线程池生命周期状态流程示意图。
  
    
  
    ![图片](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6H8321dHpzCuKh3L53K1dtbOicPuR6jWEmnE86ysW1JFvm0qiaAqkI2AB95tOHaFT8UE0uqXTmbsjg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
  
    
  
  - **ThreadPoolExecutor的参数说明**
  
    上一小节中，我们使用ThreadPoolExecutor的构造方法来创建了一个线程池。其实在ThreadPoolExecutor中有多个构造方法，但是最终都调用到了下边代码中的这一个构造方法：
  
    
  
    ```java
    public class ThreadPoolExecutor extends AbstractExecutorService {
    
        public ThreadPoolExecutor(int corePoolSize,
                                  int maximumPoolSize,
                                  long keepAliveTime,
                                  TimeUnit unit,
                                  BlockingQueue<Runnable> workQueue,
                                  ThreadFactory threadFactory,
                                  RejectedExecutionHandler handler) {
            // ...省略校验相关代码
    
            this.corePoolSize = corePoolSize;
            this.maximumPoolSize = maximumPoolSize;
            this.workQueue = workQueue;
            this.keepAliveTime = unit.toNanos(keepAliveTime);
            this.threadFactory = threadFactory;
            this.handler = handler;
        }
    
        // ...    
    
    }
    ```
  
    
  
    这个构造方法中有7个参数之多，我们逐个来看每个参数所代表的含义：
  
    
  
    - corePoolSize 表示线程池的核心线程数。当有任务提交到线程池时，如果线程池中的线程数小于corePoolSize,那么则直接创建新的线程来执行任务。
    - workQueue 任务队列，它是一个阻塞队列，用于存储来不及执行的任务的队列。当有任务提交到线程池的时候，如果线程池中的线程数大于等于corePoolSize，那么这个任务则会先被放到这个队列中，等待执行。
    - maximumPoolSize 表示线程池支持的最大线程数量。当一个任务提交到线程池时，线程池中的线程数大于corePoolSize,并且workQueue已满，那么则会创建新的线程执行任务，但是线程数要小于等于maximumPoolSize。
    - keepAliveTime 非核心线程空闲时保持存活的时间。非核心线程即workQueue满了之后，再提交任务时创建的线程，因为这些线程不是核心线程，所以它空闲时间超过keepAliveTime后则会被回收。
    - unit 非核心线程空闲时保持存活的时间的单位
    - threadFactory 创建线程的工厂，可以在这里统一处理创建线程的属性
    - handler 拒绝策略，当线程池中的线程达到maximumPoolSize线程数后且workQueue已满的情况下，再向线程池提交任务则执行对应的拒绝策略
  
    
  
  - **线程池的拒绝策略**
  
    如果线程池中的线程数达到了maximumPoolSize，并且workQueue队列存储满的情况下，线程池会执行对应的拒绝策略。在JDK中提供了RejectedExecutionHandler接口来执行拒绝操作。实现RejectedExecutionHandler的类有四个，对应了四种拒绝策略。分别如下：
  
    
  
    - DiscardPolicy 当提交任务到线程池中被拒绝时，线程池会丢弃这个被拒绝的任务
  
    - DiscardOldestPolicy 当提交任务到线程池中被拒绝时，线程池会丢弃等待队列中最老的任务。
  
    - CallerRunsPolicy 当提交任务到线程池中被拒绝时，会在线程池当前正在运行的Thread线程中处理被拒绝的任务。即哪个线程提交的任务哪个线程去执行。
  
    - AbortPolicy 当提交任务到线程池中被拒绝时，直接抛出RejectedExecutionException异常。
  
      
  
  - **线程池的工作流程**
  
    线程池提交任务是从execute方法开始的，我们可以从execute方法来分析线程池的工作流程。
  
    
  
    （1）当execute方法提交一个任务时，如果线程池中线程数小于corePoolSize,那么不管线程池中是否有空闲的线程，都会创建一个新的线程来执行任务。
  
    
  
    ![图片](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6H8321dHpzCuKh3L53K1dt8qWDDT24p6YzLnbuqiaeibuicF6iciah8Ftvaboaia4dXTgvG2FL1boE8Ykg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
  
    （2）当execute方法提交一个任务时，线程池中的线程数已经达到了corePoolSize,且此时没有空闲的线程，那么则会将任务存储到workQueue中。
  
    
  
    ![图片](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6H8321dHpzCuKh3L53K1dt45xa0iaVHMd9mehR4TlIYpjDUoKp6NcBvmNdjrTN7pDGsKPRibofhuLA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
  
    
  
    （3）如果execute提交任务时线程池中的线程数已经到达了corePoolSize,并且workQueue已满，那么则会创建新的线程来执行任务，但总线程数应该小于maximumPoolSize。
  
    
  
    ![图片](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6H8321dHpzCuKh3L53K1dtG9K6cNxlQ3gHay42kYF5bzFql9qx9tzZrk2vYZG2sbwASYPydbuptQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
  
    
  
    （4）如果线程池中的线程执行完了当前的任务，则会尝试从workQueue中取出第一个任务来执行。如果workQueue为空则会阻塞线程。
  
    
  
    ![图片](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6H8321dHpzCuKh3L53K1dt3HUtwjEqyo0zQFvBrRbd8AYXyPG28jy6OxULNGO7micON4HSMQgia1zw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
  
    
  
    （5）如果execute提交任务时，线程池中的线程数达到了maximumPoolSize，且workQueue已满，此时会执行拒绝策略来拒绝接受任务。
  
    
  
    ![图片](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6H8321dHpzCuKh3L53K1dtEIX1Z1SPibmGpbA8CX6E4o9q64x51mvjrW8l2V7AH7vpVvwuEaPtfTA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
  
    
  
    （6）如果线程池中的线程数超过了corePoolSize，那么空闲时间超过keepAliveTime的线程会被销毁，但程池中线程个数会保持为corePoolSize。
  
    
  
    ![图片](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6H8321dHpzCuKh3L53K1dtuyRBicAEsqM9ysniciak3Q5Q1L3OBAb8FXialxQ5KHqibBWdnnoAEhEkLXA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
  
    
  
    （7）如果线程池存在空闲的线程，并且设置了allowCoreThreadTimeOut为true。那么空闲时间超过keepAliveTime的线程都会被销毁。
  
    
  
    ![图片](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6H8321dHpzCuKh3L53K1dtBX7L7HMVibtoYbCLGsY3012bGhQYVcXjMMV82gGTP4PsO3ic3fbILZzA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
  
  - **源码分析**
  
  

* 参考资料
  * [ThreadPoolExecutor 官方使用说明](https://blog.csdn.net/Siobhan/article/details/51282570?ops_request_misc=%7B%22request%5Fid%22%3A%22162195034216780271552440%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fblog.%22%7D&request_id=162195034216780271552440&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_v2~rank_v29-1-51282570.pc_v2_rank_blog_default&utm_term=线程池&spm=1018.2226.3001.4450)
  * [彻底搞懂Java线程池的工作原理](https://mp.weixin.qq.com/s/tyh_kDZyLu7SDiDZppCx5Q)



### 参考资料

  * [一篇简单易懂的Java多线程基础文章](https://mp.weixin.qq.com/s?__biz=MzA5MzI3NjE2MA==&mid=2650245349&idx=1&sn=9488549af471f3f0ee8a564a3c1516d8&chksm=8863798abf14f09cc5999fd7ff9b8af31f824266513da802aa3be3d2d6c41ca9d809a8714468&xtrack=1&scene=0&subscene=131&clicktime=1553648630&ascene=7&devicetype=android-28&version=2700033b&nettype=ctnet&abtest_cookie=AwABAAoACwATAAQAI5ceAFaZHgDAmR4A3JkeAAAA&lang=zh_CN&pass_ticket=WUZw9KLx3SitUmBAZTZsUEYBCJtQDNgkE%2BNF4RSSSU4%3D&wx_header=1)





## Java 虚拟机

### 运行时的数据区域 | 内存结构

<img src="https://camo.githubusercontent.com/7318f015229248611d435ec944ded1d65b0bbd77acea11f855ebb79f1576a839/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f35373738643131332d386531332d346335332d623562662d3830316535383038306239372e706e67" alt="运行时的数据区域" style="zoom:50%;" />

* 知识点
  * **程序计数器**（它也是内存区域中唯一一个不会出现OutOfMemoryError的区域）
  
    记录正在执行的虚拟机字节码指令的地址（如果正在执行的是本地方法则为空）。
  
    
  
  * **Java虚拟机栈** （一句话便是：栈管运行，堆管存储）
  
    每个 Java 方法在执行的同时会创建一个栈帧用于存储局部变量表、操作数栈、常量池引用等信息。从方法调用直至执行完成的过程，对应着一个栈帧在 Java 虚拟机栈中入栈和出栈的过程。
  
    ![img](https://camo.githubusercontent.com/99699c80ccbb73cf078a9cbc7a5f7403375908a527e5a9d53fa23bb4366b1785/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f38343432353139662d306234642d343866342d383232392d3536663938343336336336392e706e67)
  
    
  
    可以通过 -Xss 这个虚拟机参数来指定每个线程的 Java 虚拟机栈内存大小，在 JDK 1.4 中默认为 256K，而在 JDK 1.5+ 默认为 1M：
  
    ```
    java -Xss2M HackTheJava
    ```
  
    该区域可能抛出以下异常：
  
    - 当线程请求的栈深度超过最大值，会抛出 StackOverflowError 异常；
  
    - 栈进行动态扩展时如果无法申请到足够内存，会抛出 OutOfMemoryError 异常。
  
      
  
  * **本地方法栈**
  
    本地方法栈与 Java 虚拟机栈类似，它们之间的区别只不过是本地方法栈为本地方法服务。
  
    本地方法一般是用其它语言（C、C++ 或汇编语言等）编写的，并且被编译为基于本机硬件和操作系统的程序，对待这些方法需要特别处理。
  
    ![img](https://camo.githubusercontent.com/8d83453cc5b3aef4968585931108c168b4a6910c4d586036aa6f4d696ca1a65d/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f36366136383939642d633662302d346134372d383536392d3964303866306261663836632e706e67)
  
  * **堆**
  
    所有对象都在这里分配内存，是垃圾收集的主要区域（"GC 堆"）。
  
    现代的垃圾收集器基本都是采用分代收集算法，其主要的思想是针对不同类型的对象采取不同的垃圾回收算法。可以将堆分成两块：
  
    - 新生代（Young Generation）
    - 老年代（Old Generation）
  
    堆不需要连续内存，并且可以动态增加其内存，增加失败会抛出 OutOfMemoryError 异常。
  
    可以通过 -Xms 和 -Xmx 这两个虚拟机参数来指定一个程序的堆内存大小，第一个参数设置初始值，第二个参数设置最大值。
  
    ```
    java -Xms1M -Xmx2M HackTheJava
    ```
  
    
  
  * **方法区**
  
    用于存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。
  
    和堆一样不需要连续的内存，并且可以动态扩展，动态扩展失败一样会抛出 OutOfMemoryError 异常。
  
    对这块区域进行垃圾回收的主要目标是对常量池的回收和对类的卸载，但是一般比较难实现。
  
    
  
    HotSpot 虚拟机把它当成永久代来进行垃圾回收。但很难确定永久代的大小，因为它受到很多因素影响，并且每次 Full GC 之后永久代的大小都会改变，所以经常会抛出 OutOfMemoryError 异常。为了更容易管理方法区，从 JDK 1.8 开始，移除永久代，并把方法区移至元空间，它位于本地内存中，而不是虚拟机内存中。
  
    
  
    方法区是一个 JVM 规范，永久代与元空间都是其一种实现方式。在 JDK 1.8 之后，原来永久代的数据被分到了堆和元空间中。元空间存储类的元信息，静态变量和常量池等放入堆中。
  
    
  
  * **运行时常量池**
  
    运行时常量池是方法区的一部分。
  
    Class 文件中的常量池（编译器生成的字面量和符号引用）会在类加载后被放入这个区域。
  
    除了在编译期生成的常量，还允许动态生成，例如 String 类的 intern()。
  
    
  
  * **直接内存**
  
    在 JDK 1.4 中新引入了 NIO 类，它可以使用 Native 函数库直接分配堆外内存，然后通过 Java 堆里的 DirectByteBuffer 对象作为这块内存的引用进行操作。这样能在一些场景中显著提高性能，因为避免了在堆内存和堆外内存来回拷贝数据。
  
* 参考资料
  * [大白话带你认识JVM](https://juejin.cn/post/6844904048013869064#heading-28)
  * [JVM之内存结构图文详解](https://www.huaweicloud.com/articles/4799df506d5b9ae8bd0a5cb5247723b5.html)
  * [Java虚拟机](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E8%99%9A%E6%8B%9F%E6%9C%BA.md)



### 垃圾收集

* 知识点

  垃圾收集主要是针对堆和方法区进行。程序计数器、虚拟机栈和本地方法栈这三个区域属于线程私有的，只存在于线程的生命周期内，线程结束之后就会消失，因此不需要对这三个区域进行垃圾回收。

  

  * **判断一个对象是否可被回收**

    1. 引用计数算法

       为对象添加一个引用计数器，当对象增加一个引用时计数器加 1，引用失效时计数器减 1。引用计数为 0 的对象可被回收。

       在两个对象出现循环引用的情况下，此时引用计数器永远不为 0，导致无法对它们进行回收。正是因为循环引用的存在，因此 Java 虚拟机不使用引用计数算法。

       ```
       public class Test {
       
           public Object instance = null;
       
           public static void main(String[] args) {
               Test a = new Test();
               Test b = new Test();
               a.instance = b;
               b.instance = a;
               a = null;
               b = null;
               doSomething();
           }
       }
       ```

       在上述代码中，a 与 b 引用的对象实例互相持有了对象的引用，因此当我们把对 a 对象与 b 对象的引用去除之后，由于两个对象还存在互相之间的引用，导致两个 Test 对象无法被回收。

       

    2. 可达性分析算法

       以 GC Roots 为起始点进行搜索，可达的对象都是存活的，不可达的对象可被回收。

       Java 虚拟机使用该算法来判断对象是否可被回收，GC Roots 一般包含以下内容：

       - 虚拟机栈中局部变量表中引用的对象
       - 本地方法栈中 JNI 中引用的对象
       - 方法区中类静态属性引用的对象
       - 方法区中的常量引用的对象

       ![img](https://camo.githubusercontent.com/9ec176f40c0da5eaa542cb72e85a4538f4f4a4a7afcd636d3bb00f366412f1e7/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f38336439303964322d333835382d346665312d386666342d3136343731646230623138302e706e67)

       

    3. 方法区的回收

       因为方法区主要存放永久代对象，而永久代对象的回收率比新生代低很多，所以在方法区上进行回收性价比不高。

       主要是对常量池的回收和对类的卸载。

       为了避免内存溢出，在大量使用反射和动态代理的场景都需要虚拟机具备类卸载功能。

       类的卸载条件很多，需要满足以下三个条件，并且满足了条件也不一定会被卸载：

       - 该类所有的实例都已经被回收，此时堆中不存在该类的任何实例。
       - 加载该类的 ClassLoader 已经被回收。
       - 该类对应的 Class 对象没有在任何地方被引用，也就无法在任何地方通过反射访问该类方法。

       

    4. finalize

       类似 C++ 的析构函数，用于关闭外部资源。但是 try-finally 等方式可以做得更好，并且该方法运行代价很高，不确定性大，无法保证各个对象的调用顺序，因此最好不要使用。

       当一个对象可被回收时，如果需要执行该对象的 finalize() 方法，那么就有可能在该方法中让对象重新被引用，从而实现自救。自救只能进行一次，如果回收的对象之前调用了 finalize() 方法自救，后面回收时不会再调用该方法。

    

  * **引用类型**（强引用、软引用、弱引用、虚引用）

    无论是通过引用计数算法判断对象的引用数量，还是通过可达性分析算法判断对象是否可达，判定对象是否可被回收都与引用有关。

    Java 提供了四种强度不同的引用类型。

    #### 1. 强引用

    被强引用关联的对象不会被回收。

    使用 new 一个新对象的方式来创建强引用。

    ```java
    Object obj = new Object();
    ```

    #### 2. 软引用

    被软引用关联的对象只有在内存不够的情况下才会被回收。

    使用 SoftReference 类来创建软引用。

    ```java
    Object obj = new Object();
    SoftReference<Object> sf = new SoftReference<Object>(obj);
    obj = null;  // 使对象只被软引用关联
    ```

    #### 3. 弱引用

    被弱引用关联的对象一定会被回收，也就是说它只能存活到下一次垃圾回收发生之前。

    使用 WeakReference 类来创建弱引用。

    ```java
    Object obj = new Object();
    WeakReference<Object> wf = new WeakReference<Object>(obj);
    obj = null;
    ```

    #### 4. 虚引用

    又称为幽灵引用或者幻影引用，一个对象是否有虚引用的存在，不会对其生存时间造成影响，也无法通过虚引用得到一个对象。

    为一个对象设置虚引用的唯一目的是能在这个对象被回收时收到一个系统通知。

    使用 PhantomReference 来创建虚引用。

    ```java
    Object obj = new Object();
    PhantomReference<Object> pf = new PhantomReference<Object>(obj, null);
    obj = null;
    ```

    

  * **垃圾收集算法**

    #### 1. 标记 - 清除

    ![img](https://camo.githubusercontent.com/6f13b56de16d92883b1fb764fd79306b5b1aad69699246c490a1de9bc824b816/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f30303562343831622d353032622d346533662d393835642d6430343363326233333061612e706e67)

    

    在标记阶段，程序会检查每个对象是否为活动对象，如果是活动对象，则程序会在对象头部打上标记。

    在清除阶段，会进行对象回收并取消标志位，另外，还会判断回收后的分块与前一个空闲分块是否连续，若连续，会合并这两个分块。回收对象就是把对象作为分块，连接到被称为 “空闲链表” 的单向链表，之后进行分配时只需要遍历这个空闲链表，就可以找到分块。

    在分配时，程序会搜索空闲链表寻找空间大于等于新对象大小 size 的块 block。如果它找到的块等于 size，会直接返回这个分块；如果找到的块大于 size，会将块分割成大小为 size 与 (block - size) 的两部分，返回大小为 size 的分块，并把大小为 (block - size) 的块返回给空闲链表。

    不足：

    - 标记和清除过程效率都不高；
    - 会产生大量不连续的内存碎片，导致无法给大对象分配内存。

    #### 2. 标记 - 整理

    ![img](https://camo.githubusercontent.com/ddf731a91b5f351bee653367e3567d8adf67347f30c73a085024530f8221b04e/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f63636437373361352d616433382d343032322d383935632d3761633331386633313433372e706e67)

    

    让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存。

    优点:

    - 不会产生内存碎片

    不足:

    - 需要移动大量对象，处理效率比较低。

    #### 3. 复制

    ![img](https://camo.githubusercontent.com/dfb6a5bb6fa7090467d1c6d7edaf01fce804f3b3a15bf44af511c498f24ba2e8/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f62326237376239652d393538632d343031362d386165352d3963366564643833383731652e706e67)

    

    将内存划分为大小相等的两块，每次只使用其中一块，当这一块内存用完了就将还存活的对象复制到另一块上面，然后再把使用过的内存空间进行一次清理。

    主要不足是只使用了内存的一半。

    现在的商业虚拟机都采用这种收集算法回收新生代，但是并不是划分为大小相等的两块，而是一块较大的 Eden 空间和两块较小的 Survivor 空间，每次使用 Eden 和其中一块 Survivor。在回收时，将 Eden 和 Survivor 中还存活着的对象全部复制到另一块 Survivor 上，最后清理 Eden 和使用过的那一块 Survivor。

    HotSpot 虚拟机的 Eden 和 Survivor 大小比例默认为 8:1，保证了内存的利用率达到 90%。如果每次回收有多于 10% 的对象存活，那么一块 Survivor 就不够用了，此时需要依赖于老年代进行空间分配担保，也就是借用老年代的空间存储放不下的对象。

    #### 4. 分代收集

    现在的商业虚拟机采用分代收集算法，它根据对象存活周期将内存划分为几块，不同块采用适当的收集算法。

    一般将堆分为新生代和老年代。

    - 新生代使用：复制算法
    - 老年代使用：标记 - 清除 或者 标记 - 整理 算法

    

  * **垃圾收集器**

    ![img](https://camo.githubusercontent.com/440ec4093732dc8fe7a3092666f15a7f2c4303b97499a517562e5f868fa6c967/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f63363235626161302d646465362d343439652d393364662d6333613637663266343330662e6a7067)

    

    以上是 HotSpot 虚拟机中的 7 个垃圾收集器，连线表示垃圾收集器可以配合使用。

    - 单线程与多线程：单线程指的是垃圾收集器只使用一个线程，而多线程使用多个线程；
    - 串行与并行：串行指的是垃圾收集器与用户程序交替执行，这意味着在执行垃圾收集的时候需要停顿用户程序；并行指的是垃圾收集器和用户程序同时执行。除了 CMS 和 G1 之外，其它垃圾收集器都是以串行的方式执行。

    #### 1. Serial 收集器

    ![img](https://camo.githubusercontent.com/32b65e211e072f43c474c3787c2a6f1bbf2a42dddb1a5f8899c370ce5e68804e/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f32326664613461652d346464352d343839642d616231302d3965626664616432326165302e6a7067)

    

    Serial 翻译为串行，也就是说它以串行的方式执行。

    它是单线程的收集器，只会使用一个线程进行垃圾收集工作。

    它的优点是简单高效，在单个 CPU 环境下，由于没有线程交互的开销，因此拥有最高的单线程收集效率。

    它是 Client 场景下的默认新生代收集器，因为在该场景下内存一般来说不会很大。它收集一两百兆垃圾的停顿时间可以控制在一百多毫秒以内，只要不是太频繁，这点停顿时间是可以接受的。

    #### 2. ParNew 收集器

    ![img](https://camo.githubusercontent.com/810601a45fd3edebcabe7bd13cca344466e1caa0ec36544f174086f43044555c/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f38313533386364352d316263662d346533312d383665352d6531393864663165303133622e6a7067)

    

    它是 Serial 收集器的多线程版本。

    它是 Server 场景下默认的新生代收集器，除了性能原因外，主要是因为除了 Serial 收集器，只有它能与 CMS 收集器配合使用。

    #### 3. Parallel Scavenge 收集器

    与 ParNew 一样是多线程收集器。

    其它收集器目标是尽可能缩短垃圾收集时用户线程的停顿时间，而它的目标是达到一个可控制的吞吐量，因此它被称为“吞吐量优先”收集器。这里的吞吐量指 CPU 用于运行用户程序的时间占总时间的比值。

    停顿时间越短就越适合需要与用户交互的程序，良好的响应速度能提升用户体验。而高吞吐量则可以高效率地利用 CPU 时间，尽快完成程序的运算任务，适合在后台运算而不需要太多交互的任务。

    缩短停顿时间是以牺牲吞吐量和新生代空间来换取的：新生代空间变小，垃圾回收变得频繁，导致吞吐量下降。

    可以通过一个开关参数打开 GC 自适应的调节策略（GC Ergonomics），就不需要手工指定新生代的大小（-Xmn）、Eden 和 Survivor 区的比例、晋升老年代对象年龄等细节参数了。虚拟机会根据当前系统的运行情况收集性能监控信息，动态调整这些参数以提供最合适的停顿时间或者最大的吞吐量。

    #### 4. Serial Old 收集器

    ![img](https://camo.githubusercontent.com/619f88e2830cee0ebd2cc5ca6a3ecd63e7b9ab419a91537e5ed8868ef0fd34c9/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f30386633326664332d663733362d346136372d383163612d3239356232613739373266322e6a7067)

    

    是 Serial 收集器的老年代版本，也是给 Client 场景下的虚拟机使用。如果用在 Server 场景下，它有两大用途：

    - 在 JDK 1.5 以及之前版本（Parallel Old 诞生以前）中与 Parallel Scavenge 收集器搭配使用。
    - 作为 CMS 收集器的后备预案，在并发收集发生 Concurrent Mode Failure 时使用。

    #### 5. Parallel Old 收集器

    ![img](https://camo.githubusercontent.com/224de5079435a84b00846daf60b5c0e07283cea0cd03873c511f874246d2f17d/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f32373866653433312d616638382d346139352d613839352d3963336238303131376465332e6a7067)

    是 Parallel Scavenge 收集器的老年代版本。

    在注重吞吐量以及 CPU 资源敏感的场合，都可以优先考虑 Parallel Scavenge 加 Parallel Old 收集器。

    #### 6. CMS 收集器

    ![img](https://camo.githubusercontent.com/06b53c4f9160093c891076bd6e65b31fccdbbe03fa7b23e754c7ba6e7835abda/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f36326537373939372d363935372d346236382d386431322d6266643630396262326336382e6a7067)

    

    CMS（Concurrent Mark Sweep），Mark Sweep 指的是标记 - 清除算法。

    分为以下四个流程：

    - 初始标记：仅仅只是标记一下 GC Roots 能直接关联到的对象，速度很快，需要停顿。
    - 并发标记：进行 GC Roots Tracing 的过程，它在整个回收过程中耗时最长，不需要停顿。
    - 重新标记：为了修正并发标记期间因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，需要停顿。
    - 并发清除：不需要停顿。

    在整个过程中耗时最长的并发标记和并发清除过程中，收集器线程都可以与用户线程一起工作，不需要进行停顿。

    具有以下缺点：

    - 吞吐量低：低停顿时间是以牺牲吞吐量为代价的，导致 CPU 利用率不够高。
    - 无法处理浮动垃圾，可能出现 Concurrent Mode Failure。浮动垃圾是指并发清除阶段由于用户线程继续运行而产生的垃圾，这部分垃圾只能到下一次 GC 时才能进行回收。由于浮动垃圾的存在，因此需要预留出一部分内存，意味着 CMS 收集不能像其它收集器那样等待老年代快满的时候再回收。如果预留的内存不够存放浮动垃圾，就会出现 Concurrent Mode Failure，这时虚拟机将临时启用 Serial Old 来替代 CMS。
    - 标记 - 清除算法导致的空间碎片，往往出现老年代空间剩余，但无法找到足够大连续空间来分配当前对象，不得不提前触发一次 Full GC。

    #### 7. G1 收集器

    G1（Garbage-First），它是一款面向服务端应用的垃圾收集器，在多 CPU 和大内存的场景下有很好的性能。HotSpot 开发团队赋予它的使命是未来可以替换掉 CMS 收集器。

    堆被分为新生代和老年代，其它收集器进行收集的范围都是整个新生代或者老年代，而 G1 可以直接对新生代和老年代一起回收。

    ![img](https://camo.githubusercontent.com/31d36dcfe1b2abac1d55a8dc342a117bcfe2d41c7f5a2e415f308c5735b2123e/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f34636637313161382d376162322d343135322d623835632d6435633232363733333830372e706e67)

    

    G1 把堆划分成多个大小相等的独立区域（Region），新生代和老年代不再物理隔离。

    ![img](https://camo.githubusercontent.com/ac21bcb938d3b10ec2be009a0d2aa6317163cf3af46106771f1bf5ca058ca544/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f39626264646565622d653933392d343166302d386538652d3262316130616137653061372e706e67)

    

    通过引入 Region 的概念，从而将原来的一整块内存空间划分成多个的小空间，使得每个小空间可以单独进行垃圾回收。这种划分方法带来了很大的灵活性，使得可预测的停顿时间模型成为可能。通过记录每个 Region 垃圾回收时间以及回收所获得的空间（这两个值是通过过去回收的经验获得），并维护一个优先列表，每次根据允许的收集时间，优先回收价值最大的 Region。

    每个 Region 都有一个 Remembered Set，用来记录该 Region 对象的引用对象所在的 Region。通过使用 Remembered Set，在做可达性分析的时候就可以避免全堆扫描。

    ![img](https://camo.githubusercontent.com/0f51e01c02ef3917df9a072b579a5004ae6f429dce742c32ba56d5c0de4da356/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f66393965653737312d633536662d343766622d393134382d6330303336363935623566652e6a7067)

    如果不计算维护 Remembered Set 的操作，G1 收集器的运作大致可划分为以下几个步骤：

    - 初始标记
    - 并发标记
    - 最终标记：为了修正在并发标记期间因用户程序继续运作而导致标记产生变动的那一部分标记记录，虚拟机将这段时间对象变化记录在线程的 Remembered Set Logs 里面，最终标记阶段需要把 Remembered Set Logs 的数据合并到 Remembered Set 中。这阶段需要停顿线程，但是可并行执行。
    - 筛选回收：首先对各个 Region 中的回收价值和成本进行排序，根据用户所期望的 GC 停顿时间来制定回收计划。此阶段其实也可以做到与用户程序一起并发执行，但是因为只回收一部分 Region，时间是用户可控制的，而且停顿用户线程将大幅度提高收集效率。

    具备如下特点：

    - 空间整合：整体来看是基于“标记 - 整理”算法实现的收集器，从局部（两个 Region 之间）上来看是基于“复制”算法实现的，这意味着运行期间不会产生内存空间碎片。
    - 可预测的停顿：能让使用者明确指定在一个长度为 M 毫秒的时间片段内，消耗在 GC 上的时间不得超过 N 毫秒。

    

* 参考资料
  
  * [Java虚拟机](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E8%99%9A%E6%8B%9F%E6%9C%BA.md)



### 内存分配和回收策略

* 知识点
  * Minor GC 和 Full GC
  
    - Minor GC：回收新生代，因为新生代对象存活时间很短，因此 Minor GC 会频繁执行，执行的速度一般也会比较快。
    - Full GC：回收老年代和新生代，老年代对象其存活时间长，因此 Full GC 很少执行，执行速度会比 Minor GC 慢很多。
  
    
  
  * 内存分配策略
  
    #### 1. 对象优先在 Eden 分配
  
    大多数情况下，对象在新生代 Eden 上分配，当 Eden 空间不够时，发起 Minor GC。
  
    #### 2. 大对象直接进入老年代
  
    大对象是指需要连续内存空间的对象，最典型的大对象是那种很长的字符串以及数组。
  
    经常出现大对象会提前触发垃圾收集以获取足够的连续空间分配给大对象。
  
    -XX:PretenureSizeThreshold，大于此值的对象直接在老年代分配，避免在 Eden 和 Survivor 之间的大量内存复制。
  
    #### 3. 长期存活的对象进入老年代
  
    为对象定义年龄计数器，对象在 Eden 出生并经过 Minor GC 依然存活，将移动到 Survivor 中，年龄就增加 1 岁，增加到一定年龄则移动到老年代中。
  
    -XX:MaxTenuringThreshold 用来定义年龄的阈值。
  
    #### 4. 动态对象年龄判定
  
    虚拟机并不是永远要求对象的年龄必须达到 MaxTenuringThreshold 才能晋升老年代，如果在 Survivor 中相同年龄所有对象大小的总和大于 Survivor 空间的一半，则年龄大于或等于该年龄的对象可以直接进入老年代，无需等到 MaxTenuringThreshold 中要求的年龄。
  
    #### 5. 空间分配担保
  
    在发生 Minor GC 之前，虚拟机先检查老年代最大可用的连续空间是否大于新生代所有对象总空间，如果条件成立的话，那么 Minor GC 可以确认是安全的。
  
    如果不成立的话虚拟机会查看 HandlePromotionFailure 的值是否允许担保失败，如果允许那么就会继续检查老年代最大可用的连续空间是否大于历次晋升到老年代对象的平均大小，如果大于，将尝试着进行一次 Minor GC；如果小于，或者 HandlePromotionFailure 的值不允许冒险，那么就要进行一次 Full GC。
  
    
  
  * Full GC 的触发条件
  
    对于 Minor GC，其触发条件非常简单，当 Eden 空间满时，就将触发一次 Minor GC。而 Full GC 则相对复杂，有以下条件：
  
    #### 1. 调用 System.gc()
  
    只是建议虚拟机执行 Full GC，但是虚拟机不一定真正去执行。不建议使用这种方式，而是让虚拟机管理内存。
  
    #### 2. 老年代空间不足
  
    老年代空间不足的常见场景为前文所讲的大对象直接进入老年代、长期存活的对象进入老年代等。
  
    为了避免以上原因引起的 Full GC，应当尽量不要创建过大的对象以及数组。除此之外，可以通过 -Xmn 虚拟机参数调大新生代的大小，让对象尽量在新生代被回收掉，不进入老年代。还可以通过 -XX:MaxTenuringThreshold 调大对象进入老年代的年龄，让对象在新生代多存活一段时间。
  
    #### 3. 空间分配担保失败
  
    使用复制算法的 Minor GC 需要老年代的内存空间作担保，如果担保失败会执行一次 Full GC。具体内容请参考上面的第 5 小节。
  
    #### 4. JDK 1.7 及以前的永久代空间不足
  
    在 JDK 1.7 及以前，HotSpot 虚拟机中的方法区是用永久代实现的，永久代中存放的为一些 Class 的信息、常量、静态变量等数据。
  
    当系统中要加载的类、反射的类和调用的方法较多时，永久代可能会被占满，在未配置为采用 CMS GC 的情况下也会执行 Full GC。如果经过 Full GC 仍然回收不了，那么虚拟机会抛出 java.lang.OutOfMemoryError。
  
    为避免以上原因引起的 Full GC，可采用的方法为增大永久代空间或转为使用 CMS GC。
  
    #### 5. Concurrent Mode Failure
  
    执行 CMS GC 的过程中同时有对象要放入老年代，而此时老年代空间不足（可能是 GC 过程中浮动垃圾过多导致暂时性的空间不足），便会报 Concurrent Mode Failure 错误，并触发 Full GC。
  
* 参考资料
  
  * [Java虚拟机](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E8%99%9A%E6%8B%9F%E6%9C%BA.md)



### 类加载机制

* 知识点
  * **类的生命周期**
  
    ![img](https://camo.githubusercontent.com/df6beec79a0674b90abd97c082e57d35cd6180b3b93593e33d89cb1cf8bceee1/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f33333566653139632d346137362d343561622d393332302d3838633930643661306437652e706e67)
  
    
  
    包括以下 7 个阶段：
  
    - **加载（Loading）**
    - **验证（Verification）**
    - **准备（Preparation）**
    - **解析（Resolution）**
    - **初始化（Initialization）**
    - 使用（Using）
    - 卸载（Unloading）
  
    
  
  * **类加载过程**
  
    包含了加载、验证、准备、解析和初始化这 5 个阶段。
  
    #### 1. 加载
  
    加载是类加载的一个阶段，注意不要混淆。
  
    加载过程完成以下三件事：
  
    - 通过类的完全限定名称获取定义该类的二进制字节流。
    - 将该字节流表示的静态存储结构转换为方法区的运行时存储结构。
    - 在内存中生成一个代表该类的 Class 对象，作为方法区中该类各种数据的访问入口。
  
    其中二进制字节流可以从以下方式中获取：
  
    - 从 ZIP 包读取，成为 JAR、EAR、WAR 格式的基础。
    - 从网络中获取，最典型的应用是 Applet。
    - 运行时计算生成，例如动态代理技术，在 java.lang.reflect.Proxy 使用 ProxyGenerator.generateProxyClass 的代理类的二进制字节流。
    - 由其他文件生成，例如由 JSP 文件生成对应的 Class 类。
  
    #### 2. 验证
  
    确保 Class 文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。
  
    #### 3. 准备
  
    类变量是被 static 修饰的变量，准备阶段为类变量分配内存并设置初始值，使用的是方法区的内存。
  
    实例变量不会在这阶段分配内存，它会在对象实例化时随着对象一起被分配在堆中。应该注意到，实例化不是类加载的一个过程，类加载发生在所有实例化操作之前，并且类加载只进行一次，实例化可以进行多次。
  
    初始值一般为 0 值，例如下面的类变量 value 被初始化为 0 而不是 123。
  
    ```
    public static int value = 123;
    ```
  
    如果类变量是常量，那么它将初始化为表达式所定义的值而不是 0。例如下面的常量 value 被初始化为 123 而不是 0。
  
    ```
    public static final int value = 123;
    ```
  
    #### 4. 解析
  
    将常量池的符号引用替换为直接引用的过程。
  
    其中解析过程在某些情况下可以在初始化阶段之后再开始，这是为了支持 Java 的动态绑定。
  
    #### 5. 初始化
  
    初始化阶段才真正开始执行类中定义的 Java 程序代码。初始化阶段是虚拟机执行类构造器 <clinit\>() 方法的过程。在准备阶段，类变量已经赋过一次系统要求的初始值，而在初始化阶段，根据程序员通过程序制定的主观计划去初始化类变量和其它资源。
  
    <clinit>() 是由编译器自动收集类中所有类变量的赋值动作和静态语句块中的语句合并产生的，编译器收集的顺序由语句在源文件中出现的顺序决定。特别注意的是，静态语句块只能访问到定义在它之前的类变量，定义在它之后的类变量只能赋值，不能访问。例如以下代码：
  
    ```
    public class Test {
        static {
            i = 0;                // 给变量赋值可以正常编译通过
            System.out.print(i);  // 这句编译器会提示“非法向前引用”
        }
        static int i = 1;
    }
    ```
  
    由于父类的 <clinit>() 方法先执行，也就意味着父类中定义的静态语句块的执行要优先于子类。例如以下代码：
  
    ```
    static class Parent {
        public static int A = 1;
        static {
            A = 2;
        }
    }
    
    static class Sub extends Parent {
        public static int B = A;
    }
    
    public static void main(String[] args) {
         System.out.println(Sub.B);  // 2
    }
    ```
  
    接口中不可以使用静态语句块，但仍然有类变量初始化的赋值操作，因此接口与类一样都会生成 <clinit>() 方法。但接口与类不同的是，执行接口的 <clinit>() 方法不需要先执行父接口的 <clinit>() 方法。只有当父接口中定义的变量使用时，父接口才会初始化。另外，接口的实现类在初始化时也一样不会执行接口的 <clinit>() 方法。
  
    虚拟机会保证一个类的 <clinit>() 方法在多线程环境下被正确的加锁和同步，如果多个线程同时初始化一个类，只会有一个线程执行这个类的 <clinit>() 方法，其它线程都会阻塞等待，直到活动线程执行 <clinit>() 方法完毕。如果在一个类的 <clinit>() 方法中有耗时的操作，就可能造成多个线程阻塞，在实际过程中此种阻塞很隐蔽。
  
    
  
  * **类初始化时机**
  
    ####  1. 主动引用
  
    虚拟机规范中并没有强制约束何时进行加载，但是规范严格规定了有且只有下列五种情况必须对类进行初始化（加载、验证、准备都会随之发生）：
  
    - 遇到 new、getstatic、putstatic、invokestatic 这四条字节码指令时，如果类没有进行过初始化，则必须先触发其初始化。最常见的生成这 4 条指令的场景是：使用 new 关键字实例化对象的时候；读取或设置一个类的静态字段（被 final 修饰、已在编译期把结果放入常量池的静态字段除外）的时候；以及调用一个类的静态方法的时候。
    - 使用 java.lang.reflect 包的方法对类进行反射调用的时候，如果类没有进行初始化，则需要先触发其初始化。
    - 当初始化一个类的时候，如果发现其父类还没有进行过初始化，则需要先触发其父类的初始化。
    - 当虚拟机启动时，用户需要指定一个要执行的主类（包含 main() 方法的那个类），虚拟机会先初始化这个主类；
    - 当使用 JDK 1.7 的动态语言支持时，如果一个 java.lang.invoke.MethodHandle 实例最后的解析结果为 REF_getStatic, REF_putStatic, REF_invokeStatic 的方法句柄，并且这个方法句柄所对应的类没有进行过初始化，则需要先触发其初始化；
  
    #### 2. 被动引用
  
    以上 5 种场景中的行为称为对一个类进行主动引用。除此之外，所有引用类的方式都不会触发初始化，称为被动引用。被动引用的常见例子包括：
  
    - 通过子类引用父类的静态字段，不会导致子类初始化。
  
    ```
    System.out.println(SubClass.value);  // value 字段在 SuperClass 中定义
    ```
  
    - 通过数组定义来引用类，不会触发此类的初始化。该过程会对数组类进行初始化，数组类是一个由虚拟机自动生成的、直接继承自 Object 的子类，其中包含了数组的属性和方法。
  
    ```
    SuperClass[] sca = new SuperClass[10];
    ```
  
    - 常量在编译阶段会存入调用类的常量池中，本质上并没有直接引用到定义常量的类，因此不会触发定义常量的类的初始化。
  
    ```
    System.out.println(ConstClass.HELLOWORLD);
    ```
  
    
  
  * **类与类加载器**
  
    * 两个类相等，需要类本身相等，并且使用同一个类加载器进行加载。这是因为每一个类加载器都拥有一个独立的类名称空间。
  
      这里的相等，包括类的 Class 对象的 equals() 方法、isAssignableFrom() 方法、isInstance() 方法的返回结果为 true，也包括使用 instanceof 关键字做对象所属关系判定结果为 true。
  
      
  
  * **类加载器分类**
  
    从 Java 虚拟机的角度来讲，只存在以下两种不同的类加载器：
  
    - 启动类加载器（Bootstrap ClassLoader），使用 C++ 实现，是虚拟机自身的一部分；
    - 所有其它类的加载器，使用 Java 实现，独立于虚拟机，继承自抽象类 java.lang.ClassLoader。
  
    
  
    从 Java 开发人员的角度看，类加载器可以划分得更细致一些：
  
    - 启动类加载器（Bootstrap ClassLoader）此类加载器负责将存放在 <JRE_HOME>\lib 目录中的，或者被 -Xbootclasspath 参数所指定的路径中的，并且是虚拟机识别的（仅按照文件名识别，如 rt.jar，名字不符合的类库即使放在 lib 目录中也不会被加载）类库加载到虚拟机内存中。启动类加载器无法被 Java 程序直接引用，用户在编写自定义类加载器时，如果需要把加载请求委派给启动类加载器，直接使用 null 代替即可。
    - 扩展类加载器（Extension ClassLoader）这个类加载器是由 ExtClassLoader（sun.misc.Launcher$ExtClassLoader）实现的。它负责将 <JAVA_HOME>/lib/ext 或者被 java.ext.dir 系统变量所指定路径中的所有类库加载到内存中，开发者可以直接使用扩展类加载器。
    - 应用程序类加载器（Application ClassLoader）这个类加载器是由 AppClassLoader（sun.misc.Launcher$AppClassLoader）实现的。由于这个类加载器是 ClassLoader 中的 getSystemClassLoader() 方法的返回值，因此一般称为系统类加载器。它负责加载用户类路径（ClassPath）上所指定的类库，开发者可以直接使用这个类加载器，如果应用程序中没有自定义过自己的类加载器，一般情况下这个就是程序中默认的类加载器。
  
  * 双亲委派模型（好处？）
  
    应用程序是由三种类加载器互相配合从而实现类加载，除此之外还可以加入自己定义的类加载器。
  
    
  
    下图展示了类加载器之间的层次关系，称为双亲委派模型（Parents Delegation Model）。该模型要求除了顶层的启动类加载器外，其它的类加载器都要有自己的父类加载器。这里的父子关系一般通过组合关系（Composition）来实现，而不是继承关系（Inheritance）。
  
    ![img](https://camo.githubusercontent.com/49cce8e102e0957c08390d1d29a7957fd7cbe39a3025849bfe78132aee7f739c/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f30646432643430612d356232622d346434352d623137362d6537356134636434626462662e706e67)
  
    #### 1. 工作过程
  
    一个类加载器首先将类加载请求转发到父类加载器，只有当父类加载器无法完成时才尝试自己加载。
  
    #### 2. 好处
  
    使得 Java 类随着它的类加载器一起具有一种带有优先级的层次关系，从而使得基础类得到统一。
  
    例如 java.lang.Object 存放在 rt.jar 中，如果编写另外一个 java.lang.Object 并放到 ClassPath 中，程序可以编译通过。由于双亲委派模型的存在，所以在 rt.jar 中的 Object 比在 ClassPath 中的 Object 优先级更高，这是因为 rt.jar 中的 Object 使用的是启动类加载器，而 ClassPath 中的 Object 使用的是应用程序类加载器。rt.jar 中的 Object 优先级更高，那么程序中所有的 Object 都是这个 Object。
  
    #### 3. 实现
  
    以下是抽象类 java.lang.ClassLoader 的代码片段，其中的 loadClass() 方法运行过程如下：先检查类是否已经加载过，如果没有则让父类加载器去加载。当父类加载器加载失败时抛出 ClassNotFoundException，此时尝试自己去加载。
  
    ```java
    public abstract class ClassLoader {
        // The parent class loader for delegation
        private final ClassLoader parent;
    
        public Class<?> loadClass(String name) throws ClassNotFoundException {
            return loadClass(name, false);
        }
    
        protected Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException {
            synchronized (getClassLoadingLock(name)) {
                // First, check if the class has already been loaded
                Class<?> c = findLoadedClass(name);
                if (c == null) {
                    try {
                        if (parent != null) {
                            c = parent.loadClass(name, false);
                        } else {
                            c = findBootstrapClassOrNull(name);
                        }
                    } catch (ClassNotFoundException e) {
                        // ClassNotFoundException thrown if class not found
                        // from the non-null parent class loader
                    }
    
                    if (c == null) {
                        // If still not found, then invoke findClass in order
                        // to find the class.
                        c = findClass(name);
                    }
                }
                if (resolve) {
                    resolveClass(c);
                }
                return c;
            }
        }
    
        protected Class<?> findClass(String name) throws ClassNotFoundException {
            throw new ClassNotFoundException(name);
        }
    }
    ```
  
    
  
  * **自定义类加载器实现**
  
    以下代码中的 FileSystemClassLoader 是自定义类加载器，继承自 java.lang.ClassLoader，用于加载文件系统上的类。它首先根据类的全名在文件系统上查找类的字节代码文件（.class 文件），然后读取该文件内容，最后通过 defineClass() 方法来把这些字节代码转换成 java.lang.Class 类的实例。
  
    java.lang.ClassLoader 的 loadClass() 实现了双亲委派模型的逻辑，自定义类加载器一般不去重写它，但是需要重写 findClass() 方法。
  
    ```
    public class FileSystemClassLoader extends ClassLoader {
    
        private String rootDir;
    
        public FileSystemClassLoader(String rootDir) {
            this.rootDir = rootDir;
        }
    
        protected Class<?> findClass(String name) throws ClassNotFoundException {
            byte[] classData = getClassData(name);
            if (classData == null) {
                throw new ClassNotFoundException();
            } else {
                return defineClass(name, classData, 0, classData.length);
            }
        }
    
        private byte[] getClassData(String className) {
            String path = classNameToPath(className);
            try {
                InputStream ins = new FileInputStream(path);
                ByteArrayOutputStream baos = new ByteArrayOutputStream();
                int bufferSize = 4096;
                byte[] buffer = new byte[bufferSize];
                int bytesNumRead;
                while ((bytesNumRead = ins.read(buffer)) != -1) {
                    baos.write(buffer, 0, bytesNumRead);
                }
                return baos.toByteArray();
            } catch (IOException e) {
                e.printStackTrace();
            }
            return null;
        }
    
        private String classNameToPath(String className) {
            return rootDir + File.separatorChar
                    + className.replace('.', File.separatorChar) + ".class";
        }
    }
    ```
  
  
  
* 参考资料
  
  * [Java虚拟机](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E8%99%9A%E6%8B%9F%E6%9C%BA.md)



## 学习资料

* [GitHub 星标 115k+的 Java 教程，超级硬核！](https://github.com/CyC2018/CS-Notes)
* 