# Android知识体系

 



## 公约与规范

### 代码规范

* 参考资料
  * [Android 你不能忽略的代码命名规范](https://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650826155&idx=1&sn=57e9cd17654ea8edf4a729fe4330a6c2&chksm=80b7b135b7c03823b6231e745bf6df60457d400c5a67c8dbe0f0fda4992305b57664d5bee77d&scene=0&ascene=7&devicetype=android-27&version=26070238&nettype=no&abtest_cookie=BAABAAoACwASABMABAAjlx4AU5keAFqZHgBgmR4AAAA%3D&lang=zh_CN&pass_ticket=iU4M%2BzJqW87FwaIAGHydZcm4AObeVMCMpO2j8AzL7zI%3D&wx_header=1)
  * [project_and_code_guidelines](https://github.com/ribot/android-guidelines/blob/master/project_and_code_guidelines.md)



### 代码质量工具

* 参考资料
  * [让 CodeReview 这股清流再飞一会儿](http://mp.weixin.qq.com/s?__biz=MzA3NTYzODYzMg==&mid=2653578349&idx=1&sn=b428eaf77619d3c06598d881de18e0a2&chksm=84b3b66ab3c43f7ceeb79d2345e3c40db0d9b6f62f9e13c8b89721d87b961d08e43b3caa7123&mpshare=1&scene=1&srcid=1208mDpNPvf5iDks8VMwhuWv#rd)
  * [使用Lint 和 Annotations来提升代码质量](http://www.jianshu.com/p/561998f9c3f0)
  * [[Google\] Improve Your Code with Lint](https://developer.android.com/studio/write/lint.html)
  * [[Google\] Improve Code Inspection with Annotations](https://developer.android.com/studio/write/annotations.html)
  * [提高代码质量－工具篇](http://www.jianshu.com/p/d728b25777d4)



## 1. APP 结构

### Context 

* 知识点
  * Context的使用
  
  * Context的继承关系（Context -> ContextImpl ; Context -> ContextWrapper -> Application、Activity、Service）
  
    ![context继承关系](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2017/12/15/1605838ab2813f10~tplv-t2oaga2asx-watermark.awebp)
  
  * Context功能（不同的Context类型都是调用ContextImpl的方法，但是有一些细微的差别，比如启动Activity、弹窗还是需要用Activity）
  
  * Context的数量	
  
    上面已经说过了,Context在我们常用的类型中有Application、Activity、Service三种类型因此一个应用程序中Context的数量可以这么计算：
     Context数量 = Activity数量 +Service数量 + 1（Application数量）
  
* 参考资料
  
  * [Android中Context的详细介绍](https://juejin.cn/post/6844903533989347336)



### Application

* 知识点
  * Application的使用
* 参考资料
  * [Understanding the Android Application Class](https://github.com/codepath/android_guides/wiki/Understanding-the-Android-Application-Class)




### Configuration
* 知识点
  * Configuration Changed
    * 在配置变更期间保留对象（可以通过三种方式：onSaveInstanceState()、ViewModel 和持久存储）。 
    
      [Bundle](https://developer.android.google.cn/reference/android/os/Bundle) 对象并不适合保留大量数据，因为它需要在主线程上进行序列化处理并占用系统进程内存。如需保存大量数据，您应组合使用持久性本地存储、[onSaveInstanceState()](https://developer.android.google.cn/reference/android/app/Activity#onSaveInstanceState(android.os.Bundle)) 方法和 [ViewModel](https://developer.android.google.cn/reference/androidx/lifecycle/ViewModel) 方法和 [ViewModel](https://developer.android.google.cn/reference/androidx/lifecycle/ViewModel) 类来保存数据，正如[保存界面状态](https://developer.android.google.cn/topic/libraries/architecture/saving-states)中所述。
    
      > **注意**：为了使 Android 系统恢复 Activity 中视图的状态，每个视图必须具有 `android:id` 属性提供的唯一 ID。 Activity为我们做了一定的恢复工作，Activity使用委托思想，上层委托下层，父容器委托子容器保存过程就完成了。也就是Activity -> Window -> DecorView -> 子view。
    
    * 自行处理配置变更，在Manifest的Activity tag中添加 [android:configChanges](https://developer.android.google.cn/guide/topics/manifest/activity-element#config)  ，复写Activity的 [onConfigurationChanged()](https://developer.android.google.cn/reference/android/app/Activity#onconfigurationchanged) 函数，当调用 onConfigurationChanged 的时候，Activity的Resource对象响应的会进行更新。
* 参考资料
  
  * [官网 - 处理配置变更](https://developer.android.google.cn/guide/topics/resources/runtime-changes)



### 进程和IPC

#### 进程相关知识

* UID、UserID、AppId、Pid、SharedUserID

  * android中uid用于标识一个应用程序，uid在应用安装时被分配，并且在应用存在于手机上期间，都不会改变，范围是从10000开始，到19999结束,而且，UID由用户ID(UserId)和应用ID(AppId)共同决定
  * 系统中会有多个用户 (User，即手机里的主机、访客等多用户), 每个用户也有一个唯一的 ID 值, 称为"UserId"
  * Pid就是各进程的身份标识,程序一运行系统就会自动分配给进程一个独一无二的PID
  * AppID跟app相关，包名相同的appid都一样，即使是不同用户
  * SharedUserID：通过Shared User id,拥有同一个User id的多个APK可以配置成运行在同一个进程中.所以默认就是可以互相访问任意数据. 也可以配置成运行成不同的进程, 同时可以访问其他APK的数据目录下的数据库和文件.就像访问本程序的数据一样。**必须相同签名**

* 进程的内存分配

  * 内存类型

    Android 设备包含三种不同类型的内存：RAM、zRAM 和存储器。

    RAM 是最快的内存类型，但其大小通常有限。高端设备通常具有最大的 RAM 容量

    zRAM 是用于交换空间的 RAM 分区。所有数据在放入 zRAM 时都会进行压缩，然后在从 zRAM 向外复制时进行解压。这部分 RAM 会随着页面进出 zRAM 而增大或缩小。设备制造商可以设置 zRAM 大小上限

    存储器中包含所有持久性数据（例如文件系统等），以及为所有应用、库和平台添加的代码。存储器比另外两种内存的容量大得多。在 Android 上，存储器不像在其他 Linux 实现上那样用于交换空间，因为频繁写入会导致这种内存出现损坏，并缩短存储媒介的使用寿命

    

    ![内存类型 - RAM、zRAM 和存储器](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1fbd8b8c452841c08c3f61d581b890a7~tplv-k3u1fbpfcp-watermark.awebp)

  * 内存页

    Android 的物理内存被分为多个「页」（page）。通常，每个页拥有 4KB 的内存。有三种页类型：已用页、缓存页、空闲页。

    不同类型的页有着各自的作用：

    - 已用页（**Used Pages**）

      被进程活跃使用的内存页

    - 缓存页（**Cached Pages**）

      进程正在使用的内存页，缓存页在存储器中有相应的备份，必要时可以回收

    - 空闲页（**Free Pages**）

      未使用的内存

    其中 **缓存页** 又分为 私有页 和 共享页，它们各自又分 干净页 脏页：

    - 私有页：由一个进程拥有且未共享
      - 干净页：存储器中未经修改的文件备份
      - 脏页：存储器中经过修改的文件备份
    - 共享页：由多个进程使用
      - 干净页：存储器中未经修改的文件备份
      - 脏页：存储器中经过修改的文件备份

    ![不同类型的页](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/753a21c3556a4d14afe26b5a2b919596~tplv-k3u1fbpfcp-watermark.awebp)

  * 统计内存占用

    * 常驻内存大小 (Resident Set Size - **RSS**) ： 应用使用的 **共享页 + 非共享页的数量**

    * 按比例分摊的内存大小 (Proportional Set Size - **PSS**) ： 应用使用的 **非共享页数量 + 共享页均匀分摊数量**（例如，如果三个进程共享 3MB，则每个进程的 PSS 为 1MB），**最常用**。

      可以使用 `adb shell dumpsys meminfo -s [process]` 来查看进程的 PSS
    
    * 独占内存大小 (Unique Set Size - **USS**) ： 应用使用的 **非共享页数量（不包括共享页）**

* 进程内存不足管理

  * 内核交换守护进程 （kernel swap daemon）
    * 内核交换守护进程 (`kswapd`) 是 Linux 内核的一部分，用于将已使用内存转换为可用内存。当设备上的可用内存不足时，该守护进程将变为活动状态。Linux 内核设有可用内存上下限阈值。
  * 低内存终止守护进程 （Low-memory killer）
    * 很多时候，`kswapd` 不能为系统释放足够的内存。在这种情况下，系统会使用 [`onTrimMemory()`](https://developer.android.google.cn/reference/android/content/ComponentCallbacks2?hl=zh-cn#onTrimMemory(int)) 通知应用内存不足，应该减少其分配量。如果这还不够，内核会开始终止进程以释放内存。它会使用低内存终止守护进程 (LMK) 来执行此操作。`lmk` 会每隔一段时间检查一次，当达到触发阈值时，便开始工作

* 进程类型

  * 前台进程 （有一个Activity在前台操作 或  BroadcastReceiver正在运行 或  Service正在执行某个回调）
  * 可见进程 （有Activity在前台但不可操作 或 有前台服务  或 系统正在使用其托管的某个服务）
  * 服务进程 （包含一个使用startService方法启动的Service）
  * 缓存进程 （暂时不需要用的进程）

  > 进程的优先级也可能因从属于进程的其他依赖项而提升。例如，如果进程 A 已通过 Context.BIND_AUTO_CREATE 标记绑定到 Service，或在使用进程 B 中的 ContentProvider，则进程 B 的分类始终至少和进程 A 一样重要。

* 进程优先级
  
  * oom_adj
  
    在 Android 的 `lmk` 机制中，会对于所有进程进行分类，对于每一类别的进程会有其 `oom_adj` 值的取值范围，**oom_adj 值越高则代表进程越不重要**，在系统执行低杀操作时，会从 `oom_adj` 值越高的开始杀。
  
    <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b0659f9706d044df9200bc30f11cb30f~tplv-k3u1fbpfcp-zoom-1.image" style="zoom: 50%;" />
  
  * procstate
  
    ADJ 是以 lmk 的角度对进程优先级的描述，相对比较底层。在 Java 世界中管理着 Android 四大组件和进程的是 AMS（Activity Manager Service）。AMS 对进程优先级的描述为 procstate（Process State），以变量的形式定义在 frameworks/base/core/java/android/app/ActivityManager.java 中。
  
    
  
* 杀死进程
  
  * Linux 杀进程方式
    * Linux 杀死进程的方式便是依托 SIGKILL 信号，它的值是 9。
  * Android 底层杀进程方式
    * Android 杀进程底层也是使用的信号的方式。  Process.killProcess(Process.myPid())、killProcessQuiet、killProcessGroup。
  * 上层 AMS 杀进程方式
    * AMS 中封装着杀死进程的方法，不过本质上都是上面 Process 的三个 kill 方法的调用。效果最好的方法是 **forceStopPackage**。
    * force stop 是 Android 中杀进程的一把利器，使用它可以 **杀死指定包名的进程，清理相关的四大组件，清除已注册的 alarm 和 notification**。
  
* 进程保活
  
  * persistent参数
  * 提高进程优先级
  * 杀死之后再次拉起（守护进程、push）
  
  * 加白名单（一键加速、高耗电、低内存）
  
* APP多进程实现
  
  * 如何开启多进程？如何实现私有进程？
  
    四大组件的申明的时候加上 `android:process=`标签，process名字如果直接写成“ :remote” 则说明是此前应用的子进程，其他应用的组件不可以和它跑在同一个进程中。而process名字不以“：” 开头的，则表示为全局进程，其他应用可以通过SharedUID方式和它跑在同一个进程中。
  
  * 多进程引发的问题
  
    1. 静态成员和单例模式失效
    2. 线程同步机制失效
    3. SharedPreferences的可靠性下降
    4. Application会多次创建
  
  * ShareUID
  
    两个应用通过ShareUID跑在同一个进程中是有要求的，需要这两个应用有相同的ShareUID并且签名相同才可以。
  
  

#### IPC

  * **IPC基础知识**
    
      * 序列化 （问题：有哪几种序列化的方式，哪种更好？） 
        
        | 对比         | **Serializable**                                             | **Parcelable**                                               |
        | ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
        | 所属API      | Java API                                                     | Android SDK API                                              |
        | 特点         | 序列化和反序列化会经过大量的I/O操作，产生大量的临时变量引起GC，且反序列化时需要反射 | 基于内存拷贝实现的封装和解封(marshalled& unmarshalled)，序列化基于Native层实现，不同版本的API实现可能不同 |
        | 开销         | 相对高                                                       | 相对低                                                       |
        | 效率         | 相对低                                                       | 相对高                                                       |
        | **适用场景** | **序列化到本地、网络传输**                                   | **主要内存序列化**                                           |
        
        
        
    * Binder
    
      1. Binder可以理解为一种虚拟的物理设备，他的设备驱动是 /dev/binder
    
        2. Binder调用时耗时的，客户端发起远程请求时，当前线程会被挂起直至服务器进程返回数据；服务端的Binder方法运行在Binder的线程中；因此Binder调用不能在主线程中
    
        3. Binder有两个重要的方法：linkToDeath 和 unLinkToDeath ，给BInder设置死亡代理。声明一个IBinder.DeathRecipient对象，再调用binder.linkToDeath(mDeathRecipient，0)。
    
      4. Binder连接池。设计逻辑是把所有的AIDL放在同一个Service管理。服务端提供一个queryBinder接口，这个接口能够根据业务模块的特征来返回相应的Binder对象给他们。详情可以参考《Android开发艺术探索》第2章2.5Binder连接池  章节。
    
         ![img](https://img.kancloud.cn/d4/e7/d4e7489a2008bd926464351c901dd451_1361x513.png)
    
         
    
  * **Android中IPC的方式**
    
    * 使用Bundle，1M大小限制
    * 使用文件共享
    * 使用Messenger
    * 使用ContentProvider
    * 使用AIDL
    * 使用Socket
    * 匿名共享内存
    * 使用pipe管道
    
    
    
  * AIDL（问题：如何实现、参数 in、out、inout、参数oneway）

    1. 创建 .aidl 文件，一个接口IInterface ，一个抽象类 IBinder

    2. 内部类Stub 和 代理类Proxy，Stub为服务端，Proxy为客户端

    3. 几个重要的方法：

       * DESCRIPTOR：Binder的唯一标识，一般用当前Binder的类名表示
   * asInterface：用于将服务端的Binder对象转换成客户端所需的AIDL接口类型的对象，这种转换过程是区分进程的，如果客户端和服务端位于同一进程，那么此方法返回的就是服务端的Stub对象本身，否则返回的是系统封装后的Stub.proxy对象。
       * asBinder：此方法用于返回当前Binder对象。
       * transact，onTransact：读写数据。

    4. .aidl文件中的参数in代表只能由客户端流向服务端；out 表示数据只能由服务端流向客户端；inout 则表示数据可在服务端与客户端之间双向流通；**oneway关键字用于修改远程调用的行为，**被oneway修饰了的方法不可以有返回值，也不可以有带out或inout的参数


​       

* RemoteCallbackList

  RemoteCallbackList是系统专门提供用于删除跨进程listener的接口。使用registerListener和unregisgerListener 来注册和反注册，使用beginBroadcast和finishBroadcast配对使用来通知回调。



* Binder 和 Intent传递的数据大小

  Binder 事务缓冲区的大小固定有限，目前为 1MB，由进程中正在处理的所有事务共享。由于此限制是进程级别而不是 Activity 级别的限制，因此这些事务包括应用中的所有 binder 事务，例如 onSaveInstanceState，startActivity 以及与系统的任何互动。超过大小限制时，将引发TransactionTooLargeException。

  参考：[intent传递数据大小限制](https://www.jianshu.com/p/2182ae72e56b) 、[Parcelable 和 Bundle - 官网](https://developer.android.google.cn/guide/components/activities/parcelables-and-bundles?hl=zh-cn)




#### Binder

* 原理
* 参考资料
  * [Binder十万个为什么](http://vanelst.site/2020/08/07/binder-question/)
  * [彻底理解Android Binder通信架构](http://gityuan.com/2016/09/04/binder-start-service/)
  * [Android跨进程通信：图文详解 Binder机制 原理](https://blog.csdn.net/carson_ho/article/details/73560642)



#### 参考资料
* 《Android开发艺术探索》
* [进程和应用生命周期](https://developer.android.google.cn/guide/components/activities/process-lifecycle)
* [Android官网 - 管理内存](https://developer.android.google.cn/topic/performance/memory-overview?hl=zh-cn)
* [Android Detail：进程篇——关于进程你需要了解这些](https://xiaozhuanlan.com/topic/2036195874)    | [Android Detail：进程篇——进程内存分配与优先级](https://juejin.cn/post/6891911483379482637)
* [Android AIDL参数中in、out、inout、oneway含义及区别](http://nicethemes.cn/news/txtlist_i6454v.html)
* [2020年了，Android后台保活还有戏吗？看我如何优雅的实现！](http://www.52im.net/thread-2881-1-1.html)
* [Android序列化(Serializable/Parcelable)总结](https://blog.csdn.net/u013700502/article/details/104161991)



### 主题样式

* 知识点
  * <font color='red'> TODO</font>
* 参考资料
  * [Styles and Themes -  CodePath - 使用篇](https://guides.codepath.com/android/Styles-and-Themes)



### 编译&打包

* 知识点
  * **构建流程总览**

    <img src="https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/23/16ae50ae0b37d1d4~tplv-t2oaga2asx-watermark.awebp" alt="img" style="zoom:50%;" />

    ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/23/16ae50ac45a2cda9~tplv-t2oaga2asx-watermark.awebp)

    如图所示apk打包流程主要分为以下几个阶段：

    1. aapt阶段，打包资源文件，生成R.java文件
    2. aidl阶段，处理aidl文件，生成相应的.java文件
    3. Java Compiler阶段，编译工程源码，生成相应的class文件
    4. dex阶段，转换所有的class文件，生成classes.dex文件
    5. apkbuilder阶段，打包生成apk
    6. Jarsigner阶段，对apk文件进行签名
    7. zipalign阶段，对签名后的apk进行对齐处理，这个过程一般是release包条件下。
       

    每一个阶段，都对应一个工具：

    ![apk打包所需工具](https://img-blog.csdnimg.cn/20210331234314164.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpMDk3OA==,size_16,color_FFFFFF,t_70#pic_center)

    

    **proguard 、D8 、 RB** 在将class转化为dex的过程。

    

    

    

  * **Android  apk 运行的过程**

    * Dalvik （Interpreter & JIT）

      `APK`安装文件一般包含了所有的资源文件（图片，图标和布局等）和代码文件，安装时系统会把这些资源存储到内存中。当用户点击应用图标时，手机会启动一个`Dalvik`进程，然后将该`app`的`dex`文件加载到内存，同时`Dalvik`虚拟机将会把`dex`字节码通过`Interpreter`或`JIT`翻译成机器码，最终这款`app`运行在了你手机上。

      ![APP 运行过程](https://kingsfish.github.io/2019/10/03/%E8%B0%88%E8%B0%88Android%E7%BC%96%E8%AF%91%E8%BF%90%E8%A1%8C%E8%BF%87%E7%A8%8B/running.png)

    * ART （AOT）

      `ART`使用`AOT（Ahead of Time）`编译器来将字节码翻译成`.oat`文件。`APP`被安装时，`ART`将`Dex`字节码预编译成`.oat`文件，每次启动`APP`时系统直接读取`.oat`文件运行即可，不再使用`JIT`以及`Interpreter`这种即时翻译的工具，大大提升了运行流畅度。

      这样看起来好像`AOT`好像没什么毛病，`Google`当初也觉得自己找到了终极解决方式，但实际上`AOT`还是会有一些问题：
  
      - `AOT`在安装过程中进行预编译行为，这样安装和更新`APP`时间相比原来就会大大增加。另外，升级`Android`系统时，系统会把所有程序重新安装一次，想想那么多`APP`要重新安装编译成`.oat`文件，头都大了。
      - 空间大小。`AOT`将整个`.dex`文件都翻译为`.oat`文件，包括那种很少使用或者根本不会被使用到的代码（比如第一次打开`APP`的设置向导或者开屏界面）。根据`Google`相关的数据，常用的代码大概占所有代码的`15~20%`左右，这样就浪费了很大一部分空间，在一些小存储容量的低端机上这个问题尤其明显。

      

      既然这也不好，那也不好，那就集中两种方式的优点就好了。`Google`工程师想出了一个新点子：`Interpreter + JIT + AOT`混合编译，具体方案如下：
  
      1. 安装的时候不进行预编译，也即不生成`.oat`文件。`app`第一次启动时，`ART`使用`Interpreter`来实时翻译`.dex`文件。
      2. 当出现`Hot Code`时，使用`JIT`进行翻译，并将翻译后的机器码存入缓存（内存）中，之后调用`Hot Code`时直接从缓存中取。
      3. 当设备空闲时，比如锁屏，`Hot Code`会被`AOT`编译器编译成`oat`文件存入本地存储空间。
      4. `app`再次启动时，如果存在`.oat`文件，那么直接使用`.oat`文件，否则从步骤1开始。

      ![AOT 运行过程](https://kingsfish.github.io/2019/10/03/%E8%B0%88%E8%B0%88Android%E7%BC%96%E8%AF%91%E8%BF%90%E8%A1%8C%E8%BF%87%E7%A8%8B/oatRun.png)

      AOT 运行过程
  
      国内的厂商会有“基于用户操作习惯进行学习，APP打开速度不断提高
      ”的说法，有一部分是这个混合编译方案的功劳。

      

      dex2oat 与 dexopt

    

    

    * PGO

      在说`AOT`混合编译的时候系统会生成一个`profile`，这个`profile`记录了`hotcode`的信息，哪些类和哪些方法会被经常调用。而对于大多数人来说，同一个`APP`的`hotcode`区别不大，其实可以共用，因此`Google`在`2018 Google I/O`大会上提出了`Cloud Profiles`的方案。具体原理如下：
  
      ![共享](https://kingsfish.github.io/2019/10/03/%E8%B0%88%E8%B0%88Android%E7%BC%96%E8%AF%91%E8%BF%90%E8%A1%8C%E8%BF%87%E7%A8%8B/share.png)
    
      
    
      这个方案依赖`Google Play`来完成。当一个设备为空闲状态并且连接到`WiFi`时，`Google Play Service`会将编译后的文件共享，之后如果有一样的手机从`Googole Play`中下载这个`APP`时，终端会收到其他人的`hotcode`信息，这样用户在第一次使用时就能获得良好的体验。
    
      但实际上，一个人的`hotcode`无法代表所有人的`hotcode`信息，那么需要多少个样本才能拿到一个比较稳定的`hotcode profile`呢？根据官方的数据，这个数字还挺小的。
    
      
    
  * [**Gradle dependencies**](https://developer.android.google.cn/studio/build/dependencies)  
  
    | 配置                  | 行为                                                         |
    | :-------------------- | :----------------------------------------------------------- |
    | `implementation`      | Gradle 会将依赖项添加到编译类路径，并将依赖项打包到构建输出。不过，当您的模块配置 `implementation` 依赖项时，会让 Gradle 了解您不希望该模块在编译时将该依赖项泄露给其他模块。也就是说，其他模块只有在运行时才能使用该依赖项。<br /><br />使用此依赖项配置代替 `api` 或 `compile`（已弃用）可以**显著缩短构建时间**，因为这样可以减少构建系统需要重新编译的模块数。例如，如果 `implementation` 依赖项更改了其 API，Gradle 只会重新编译该依赖项以及直接依赖于它的模块。大多数应用和测试模块都应使用此配置。 |
    | `api`                 | Gradle 会将依赖项添加到编译类路径和构建输出。当一个模块包含 `api` 依赖项时，会让 Gradle 了解该模块要以传递方式将该依赖项导出到其他模块，以便这些模块在运行时和编译时都可以使用该依赖项。此配置的行为类似于 `compile`（现已弃用），但使用它时应格外小心，只能对您需要以传递方式导出到其他上游消费者的依赖项使用它。这是因为，如果 `api` 依赖项更改了其外部 API，Gradle 会在编译时重新编译所有有权访问该依赖项的模块。因此，拥有大量的 `api` 依赖项会显著增加构建时间。除非要将依赖项的 API 公开给单独的模块，否则库模块应改用 `implementation` 依赖项。 |
    | `compileOnly`         | Gradle 只会将依赖项添加到编译类路径（也就是说，不会将其添加到构建输出）。如果您创建 Android 模块时在编译期间需要相应依赖项，但它在运行时可有可无，此配置会很有用。如果您使用此配置，那么您的库模块必须包含一个运行时条件，用于检查是否提供了相应依赖项，然后适当地改变该模块的行为，以使该模块在未提供相应依赖项的情况下仍可正常运行。这样做不会添加不重要的瞬时依赖项，因而有助于减小最终 APK 的大小。此配置的行为类似于 `provided`（现已弃用）。**注意**：您不能将 `compileOnly` 配置与 AAR 依赖项配合使用。 |
    | `runtimeOnly`         | Gradle 只会将依赖项添加到构建输出，以便在运行时使用。也就是说，不会将其添加到编译类路径。此配置的行为类似于 `apk`（现已弃用）。 |
    | `annotationProcessor` | 如需添加对作为注解处理器的库的依赖，您必须使用 `annotationProcessor` 配置将其添加到注解处理器的类路径。这是因为，使用此配置可以将编译类路径与注释处理器类路径分开，从而提高构建性能。如果 Gradle 在编译类路径上找到注释处理器，则会禁用[避免编译](https://docs.gradle.org/current/userguide/java_plugin.html#sec:java_compile_avoidance)功能，这样会对构建时间产生负面影响（Gradle 5.0 及更高版本会忽略在编译类路径上找到的注释处理器）。如果 JAR 文件包含以下文件，则 Android Gradle 插件会假定依赖项是注释处理器： `META-INF/services/javax.annotation.processing.Processor`。 如果插件检测到编译类路径上包含注解处理器，则会产生构建错误。**注意**：Kotlin 项目应[使用 kapt](https://kotlinlang.org/docs/reference/kapt.html) 声明注解处理器依赖项。 |
    | `lintChecks`          | 使用此配置可以添加您希望 Gradle 在构建项目时执行的 lint 检查。**注意**：使用 Android Gradle 插件 3.4.0 及更高版本时，此依赖项配置不再将 lint 检查打包在 Android 库项目中。如需将 lint 检查依赖项包含在 AAR 库中，请使用下面介绍的 `lintPublish` 配置。 |
    | `lintPublish`         | 在 Android 库项目中使用此配置可以添加您希望 Gradle 编译成 `lint.jar` 文件并打包在 AAR 中的 lint 检查。这会使得使用 AAR 的项目也应用这些 lint 检查。如果您之前使用 `lintChecks` 依赖项配置将 lint 检查添加到已发布的 AAR 中，则需要迁移这些依赖项以改用 `lintPublish` 配置。<br /><br />```xmldependencies {<br/>  // Executes lint checks from the ':checks' project<br/>  // at build time.<br/>  lintChecks project(':checks')<br/>  // Compiles lint checks from the ':checks-to-publish'<br/>  // into a lint.jar file and publishes it to your<br/>  // Android library.<br/>  lintPublish project(':checks-to-publish')<br/>}``` |
  
* 参考资料
  
  * [浅谈Android打包流程](https://juejin.cn/post/6844903850453762055)
  * [浅谈Android编译打包流程](https://blog.csdn.net/li0978/article/details/115364193)
  * [谈谈Android编译运行过程](https://kingsfish.github.io/2019/10/03/%E8%B0%88%E8%B0%88Android%E7%BC%96%E8%AF%91%E8%BF%90%E8%A1%8C%E8%BF%87%E7%A8%8B/)
  * [配置构建 - Android官网](https://developer.android.google.cn/studio/build/)
  * [Android 代码混淆，到底做了什么？](https://mp.weixin.qq.com/s/zi3K7lXfSodHj6tUTLKxgg)
  * [添加构建依赖项](https://developer.android.google.cn/studio/build/dependencies)



## 2. APP组件知识

### Activity

* 知识点
  * Activity的生命周期

    * 典型情况下的生命周期

      > 其中onRestart只有用户自己返回的时候才会调用。

      ![](https://developer.android.google.cn/guide/components/images/activity_lifecycle.png?hl=zh-cn)

      

    * 异常情况下的生命周期

      异常生命周期会调用onSaveInstanceState 和 onRestoreInstanceState 函数，主要是Config发生变化、内存不足导致Activity被杀的时候会调用以上两个函数。

  * Activity的启动模式

    * Activity的LaunchMode
  
      1. standand（标准模式）
      2. singleTop（栈顶复用模式）
      3. singleTask（栈内复用模式）
      4. singleInstance（单实例模式）
  
      > TaskAffinity（相关性），默认情况下，所有的Activity所属的任务栈的名字为应用包名。也可以单独指定TaskAffinity属性，这个属性值不能和包名相同，否则相当于没指定。
      >
      > 
      >
      > TaskAffinity主要是和singleTask启动模式或者allowTaskReparenting属性配对使用，在其他情况下没有意义。当 Activity 的 [`allowTaskReparenting`](https://developer.android.google.cn/guide/topics/manifest/activity-element?hl=zh-cn#reparent) 属性设为 `"true"` 时。在这种情况下，一旦和 Activity 有亲和性的任务进入前台运行，Activity 就可从其启动的任务转移到该任务。

    * Activity的Flags

      1.  [FLAG_ACTIVITY_NEW_TASK](https://developer.android.google.cn/reference/android/content/Intent?hl=zh-cn#FLAG_ACTIVITY_NEW_TASK) ： 和 `"singleTask"` [`launchMode`](https://developer.android.google.cn/guide/topics/manifest/activity-element?hl=zh-cn#lmode) 值产生的行为相同

      2. [FLAG_ACTIVITY_SINGLE_TOP](https://developer.android.google.cn/reference/android/content/Intent?hl=zh-cn#FLAG_ACTIVITY_SINGLE_TOP) ： 和  `"singleTop"` [`launchMode`](https://developer.android.google.cn/guide/topics/manifest/activity-element?hl=zh-cn#lmode) 值产生的行为相同

      3. [FLAG_ACTIVITY_CLEAR_TOP](https://developer.android.google.cn/reference/android/content/Intent?hl=zh-cn#FLAG_ACTIVITY_CLEAR_TOP) ： 如果要启动的 Activity 已经在当前任务中运行，则不会启动该 Activity 的新实例，而是会销毁位于它之上的所有其他 Activity，并通过 `onNewIntent()` 将此 intent 传送给它的已恢复实例（现在位于堆栈顶部）。（`FLAG_ACTIVITY_CLEAR_TOP` 最常与 `FLAG_ACTIVITY_NEW_TASK` 结合使用。将这两个标记结合使用，可以查找其他任务中的现有 Activity，并将其置于能够响应 intent 的位置。）

         > **注意**：如果指定 Activity 的启动模式为 `"standard"`，系统也会将其从堆栈中移除，并在它的位置启动一个新实例来处理传入的 intent。这是因为当启动模式为 `"standard"` 时，始终会为新 intent 创建新的实例。

    * Intent中addFlags的方式优先级要高于LaunchMode，两种同时存在的时候以addFlags为准。addFlags无法设置singleInstance模式，而LaunchMode无法使用FLAG_ACTIVITY_CLEAR_TOP

  * IntentFilter的匹配规则

    一个Intent匹配任何一组IntentFilter即可成功启动对应的Activity。一组IntentFilter中Action、category和data可以有多个，构成不同类别，统一类别的信息共同约束当前类别的匹配过程。只有一个Intent同时匹配action类别、category类别、data类别才算完全匹配。

    * action的匹配规则

      action中的匹配要求Intent中的action存在且必须和过滤规则中的其中一个action相同，和category的匹配规则不同。

    * category的匹配规则

      category的匹配规则和action不同，要求Intent中如果有category，那么所有的category都必须和过滤规则中的其中一个category相同。也就是说Inent中如果出现了category，不管有几个category，对于每个category来说，它必须是过滤规则中已经定义的category。

      > 不设置category也可以，原因是系统启动Activity的时候会默认给我加上"android.intent.category.DEFAULT"，所以隐式的Activity必须加上"android.intent.category.DEFAULT"这个category。

    * data的匹配规则

      如果定义了data，那么Intent中必须也要定义可匹配的data。

      data由两部分组成，mimeType和URI。mimeType指媒体类型，URI包括Scheme、Host、Port、Path。`URI的schema是有默认值的，为content和file`。
* 参考资料
  * 《Android开发艺术探索》
  * [官方文档 - Activity生命周期](https://developer.android.google.cn/guide/components/activities/activity-lifecycle?hl=zh-cn)
  * [不怕面试再问 Activity，一次彻底地梳理清楚！](https://mp.weixin.qq.com/s/FdfBfyePoX2BI5OkXtmICA)



### Broadcast

* 知识点

  * 系统广播的变更

    * 从 Android 9（API 级别 28）开始，[`NETWORK_STATE_CHANGED_ACTION`](https://developer.android.com/reference/android/net/wifi/WifiManager#NETWORK_STATE_CHANGED_ACTION) 广播不再接收有关用户位置或个人身份数据的信息。
    * 从 Android 8.0（API 级别 26）开始，系统对清单声明的接收器施加了额外的限制。如果您的应用以 Android 8.0 或更高版本为目标平台，那么对于大多数隐式广播（没有明确针对您的应用的广播），您不能使用清单来声明接收器。当用户正在活跃地使用您的应用时，您仍可使用[上下文注册的接收器](https://developer.android.com/guide/components/broadcasts#context-registered-recievers)。
    * Android 7.0（API 级别 24）及更高版本不发送以下系统广播：ACTION_NEW_PICTURE 、ACTION_NEW_VIDEO。

  * 接收广播

    * 清单申明

    * 动态注册

    * 对进程状态的影响

      当进程执行接收器（即当前在运行其 `onReceive()` 方法中的代码）时，它被认为是前台进程。但是，一旦从 `onReceive()` 返回代码，BroadcastReceiver 就不再活跃。

      > 您不应从广播接收器启动长时间运行的后台线程。`onReceive()` 完成后，系统可以随时终止进程来回收内存，在此过程中，也会终止进程中运行的派生线程。要避免这种情况，您应该调用 `goAsync()`（如果您希望在后台线程中多花一点时间来处理广播）或者使用 `JobScheduler` 从接收器调度 `JobService`，这样系统就会知道该进程将继续活跃地工作。

  * 发送广播

    * `sendOrderedBroadcast(Intent, String)` 方法一次向一个接收器发送广播。当接收器逐个顺序执行时，接收器可以向下传递结果，也可以完全中止广播，使其不再传递给其他接收器。接收器的运行顺序可以通过匹配的 intent-filter 的 android:priority 属性来控制；具有相同优先级的接收器将按随机顺序运行。
    * `sendBroadcast(Intent)` 方法会按随机的顺序向所有接收器发送广播。这称为常规广播。这种方法效率更高，但也意味着接收器无法从其他接收器读取结果，无法传递从广播中收到的数据，也无法中止广播。
    * `LocalBroadcastManager.sendBroadcast` 方法会将广播发送给与发送器位于同一应用中的接收器。如果您不需要跨应用发送广播，请使用本地广播。这种实现方法的效率更高（无需进行进程间通信），而且您无需担心其他应用在收发您的广播时带来的任何安全问题。

  * 发送、接收广播如何限制

    * 带权限发送
    * 带权限接收

  * 注意事项（安全权限、生命周期、性能效率）

    * `LocalBroadcastManager` 效率更高（无需进行进程间通信），并且您无需考虑其他应用在收发您的广播时带来的任何安全问题。
    * 请优先使用上下文注册而不是清单声明
    * 请勿使用隐式 intent 广播敏感信息
    * 当您注册接收器时，任何应用都可以向您应用的接收器发送潜在的恶意广播
    * 广播操作的命名空间是全局性的。请确保在您自己的命名空间中编写操作名称和其他字符串，否则可能会无意中与其他应用发生冲突。
    * 在广播中如果涉及到耗时操作，请使用[goAsync()](https://developer.android.com/reference/android/content/BroadcastReceiver#goAsync()) 或 [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler)。
    * 请勿从广播接收器启动 Activity，否则会影响用户体验，尤其是有多个接收器时。相反，可以考虑显示[通知](https://developer.android.com/guide/topics/ui/notifiers/notifications)。

* 参考资料

  * [Broadcast - 官网文档](https://developer.android.com/guide/components/broadcasts) 



### Service

* 知识点
  * 创建服务

    * `onStartCommand()` 方法必须返回整型数。整型数是一个值，用于描述系统应如何在系统终止服务的情况下继续运行服务。

      > ```
      > START_NOT_STICKY
      > ```
      >
      > 如果系统在 `onStartCommand()` 返回后终止服务，则除非有待传递的挂起 Intent，否则系统*不会*重建服务。这是最安全的选项，可以避免在不必要时以及应用能够轻松重启所有未完成的作业时运行服务。
      >
      > ```
      > START_STICKY
      > ```
      >
      > 如果系统在 `onStartCommand()` 返回后终止服务，则其会重建服务并调用 `onStartCommand()`，但*不会*重新传递最后一个 Intent。相反，除非有挂起 Intent 要启动服务，否则系统会调用包含空 Intent 的 `onStartCommand()`。在此情况下，系统会传递这些 Intent。此常量适用于不执行命令、但无限期运行并等待作业的媒体播放器（或类似服务）。
      >
      > ```
      > START_REDELIVER_INTENT
      > ```
      >
      > 如果系统在 `onStartCommand()` 返回后终止服务，则其会重建服务，并通过传递给服务的最后一个 Intent 调用 `onStartCommand()`。所有挂起 Intent 均依次传递。此常量适用于主动执行应立即恢复的作业（例如下载文件）的服务。

  * 启动方式（StartService、BindService）

  * 前台服务

    * 如要请求让服务在前台运行，请调用 `startForeground()`
    * Android 9 及以上需要申明使用 [`FOREGROUND_SERVICE`](https://developer.android.com/reference/android/Manifest.permission#FOREGROUND_SERVICE) 权限

  * 绑定服务（会抛异常）

    * 创建绑定服务的方式

      1. 扩展 Binder 类
      2. 使用 Messenger
      3. 使用AIDL

    * 绑定到服务

      1. 实现 `ServiceConnection` 。

         >  onServiceDisconnected() 当与服务的连接意外中断时，例如服务崩溃或被终止时，Android 系统会调用该方法。当客户端取消绑定时，系统不会调用该方法。

      2. 调用 `bindService()`，从而传递 `ServiceConnection` 实现

      3. 当系统调用 `onServiceConnected()` 回调方法时，您可以使用接口定义的方法开始调用服务

      4. 如要断开与服务的连接，请调用 `unbindService()`

      > 您应该始终捕获 `DeadObjectException` 异常，系统会在连接中断时抛出此异常。这是远程方法抛出的唯一异常。

  * AIDL 

    [参考 IPC 章节](#进程和IPC)
* 参考资料
  * [Service - 官网文档](https://developer.android.com/guide/components/services) 
  * [Service自己的总结]( https://blog.csdn.net/Siobhan/article/details/51315974)  



### Permission

* 知识点
  * 权限的类型
    1. 安装时权限

    2. 普通权限

       > 此类权限允许访问超出应用沙盒的数据和执行超出应用沙盒的操作。但是，这些数据和操作对用户隐私及对其他应用的操作带来的风险非常小。

    3. 签名权限

       > 当应用声明了其他应用已定义的签名权限时，如果两个应用使用同一证书进行签名，系统会在安装时向前者授予该权限。否则，系统无法向前者授予该权限。

    4. 运行时权限

    5. 特殊权限
  * 最佳做法
    1. 请求最少数量的权限
    2. 将运行时权限与特定操作相关联
    3. 考虑应用的依赖项
    4. 公开透明
    5. 以显式方式访问系统
* 参考资料
  
  * [官网文档 - Android中的权限](https://developer.android.com/guide/topics/permissions/overview#minimal-number)



### Fragment

* 知识点
  * 创建和使用Fragment

    * 在xml中使用，使用`FragmentContainerView` 来占位。
    * 在代码中直接加载。

  * Fragment管理器

    1. 访问FragmentManager

       * 在`Activity`中访问， 调用 [`getSupportFragmentManager()`](https://developer.android.com/reference/androidx/fragment/app/FragmentActivity#getSupportFragmentManager()) 方法访问 `FragmentManager`。

       * 在Fragment中访问，Fragment 也能够托管一个或多个子 Fragment。在 Fragment 内，您可以通过 [`getChildFragmentManager()`](https://developer.android.com/reference/androidx/fragment/app/Fragment#getChildFragmentManager()) 获取对管理 Fragment 子级的 `FragmentManager` 的引用。如果您需要访问其宿主 `FragmentManager`，可以使用 [`getParentFragmentManager()`](https://developer.android.com/reference/androidx/fragment/app/Fragment#getParentFragmentManager())。

       <img src="https://developer.android.google.cn/images/guide/fragments/manager-mappings.png" style="zoom: 50%;" />

    2. 使用FragmentManager （FragmentTransaction）

       * FragmentManager 管理 Fragment 返回堆栈。在运行时，FragmentManager 可以执行添加或移除 Fragment 等返回堆栈操作来响应用户互动。每一组更改作为一个单元（称为 FragmentTransaction）一起提交。
       * [setReorderingAllowed(true)](https://developer.android.google.cn/reference/androidx/fragment/app/FragmentTransaction#setReorderingAllowed(boolean)) 可优化事务中涉及的 Fragment 的状态变化，以使动画和过渡正常运行。
       * 调用 [addToBackStack()](https://developer.android.google.cn/reference/androidx/fragment/app/FragmentTransaction#addToBackStack(java.lang.String)) 会将事务提交到返回堆栈。用户稍后可以通过按“返回”按钮反转事务并恢复上一个 Fragment。如果您在一个事务中添加或移除了多个 Fragment，弹出返回堆栈时，所有这些操作都会撤消。在 `addToBackStack()` 调用中提供的可选名称使您能够使用 [popBackStack()](https://developer.android.google.cn/reference/androidx/fragment/app/FragmentManager#popBackStack(java.lang.String, int)) 弹回到该特定事务。
       * `FragmentManager` 可让您通过 `saveBackStack()` 和 `restoreBackStack()` 方法**支持多个返回堆栈**。这两种方法使您可以通过保存一个返回堆栈并恢复另一个返回堆栈来在返回堆栈之间进行交换。

    3. 查找现有Fragment

       * [`findFragmentById()`](https://developer.android.com/reference/androidx/fragment/app/FragmentManager#findFragmentById(int)) 
       * [`findFragmentByTag()`](https://developer.android.com/reference/androidx/fragment/app/FragmentManager#findFragmentByTag(java.lang.String)) 

  * Fragment之间添加过度动画效果

    1. 设置Animation动画
       * 使用[`FragmentTransaction.setCustomAnimations()`](https://developer.android.com/reference/androidx/fragment/app/FragmentTransaction#setCustomAnimations(int, int))方法
    2. Transitions动画

  * 生命周期

    <img src="https://developer.android.google.cn/images/guide/fragments/fragment-view-lifecycle.png" style="zoom:50%;" />

    2. *使用 addToBackStack()之后的生命周期？*
    2. *使用ViewPager2的生命周期*

  * 与Fragment通信

    1. `Fragment` 库提供了两个通信选项：共享的 [`ViewModel`](https://developer.android.com/topic/libraries/architecture/viewmodel) 和 Fragment Result API。建议的选项取决于用例。如需与任何自定义 API 共享持久性数据，您应使用 `ViewModel`。对于包含的数据可放置在 [`Bundle`](https://developer.android.com/reference/android/os/Bundle) 中的一次性结果，您应使用 Fragment Result API（ setFragmentResultListener() +   setFragmentResult）。

  * ViewPager与 Fragment

    1. ViewPagerFragmentPagerAdapter 和 FragmentStatePagerAdapter
       * FragmentPagerAdapter把每一页都表现为一个Fragment，并且会被保留在fragment manager 中。
         FragmentPagerAdapter比较适合于少量静态Fragment之间的切换, 比如一套Tab。每一个用户浏览过的Fragment会被保存在内存中，即使它的view已经destroy了。这会导致 使用大量的内存，因为Fragment的实体会被保存到任意的状态。
       * FragmentStatePagerAdapter使用一个Fragment管理每个page。这个类同时也操控保存和恢复fragment的状态。
         这个类适合于有大量pages的时候，比如一个列表视图。当page不可见的时候，它们的fragment也会被销毁，只会保留被保存的状态。 这种模式比FragmentPagerAdapter 使用更少的内存，但是在切换的时候会有更多的消耗。
    2. 切换时的生命周期

  * ViewPager2 与 Fragment 

    1. Viewpager2比ViewPager的优势

       * 基于RecyclerView实现
       * 垂直方向支持
       * 支持RTL
       * 可以修改Fragment的集合
     * DiffUtil （`ViewPager2` 在 [`RecyclerView`](https://developer.android.com/reference/kotlin/androidx/recyclerview/widget/RecyclerView) 的基础上构建而成，这意味着它可以访问 [`DiffUtil`](https://developer.android.com/reference/kotlin/androidx/recyclerview/widget/DiffUtil) 实用程序类。这一点带来了多项优势，但最突出的一项是，这意味着 `ViewPager2` 对象本身会利用 `RecyclerView` 类中的数据集更改动画。）
    
  2. 如何使用ViewPager2
    
     * 适配器类
     
       对于要转换为 `ViewPager2` 对象的每个 `ViewPager` 对象，请更新适配器类以扩展相应的抽象类，如下所示：
     
         - 当 `ViewPager` 使用 [`PagerAdapter`](https://developer.android.com/reference/kotlin/androidx/viewpager/widget/PagerAdapter) 分页浏览视图时，将 [`RecyclerView.Adapter`](https://developer.android.com/reference/kotlin/androidx/recyclerview/widget/RecyclerView.Adapter) 用于 `ViewPager2`。
         - 当 `ViewPager` 使用 [`FragmentPagerAdapter`](https://developer.android.com/reference/kotlin/androidx/fragment/app/FragmentPagerAdapter) 分页浏览固定数量的较少 Fragment 时，将 [`FragmentStateAdapter`](https://developer.android.com/reference/kotlin/androidx/viewpager2/adapter/FragmentStateAdapter) 用于 `ViewPager2`。
       - 当 `ViewPager` 使用 [`FragmentStatePagerAdapter`](https://developer.android.com/reference/kotlin/androidx/fragment/app/FragmentStatePagerAdapter) 分页浏览大量或未知数量的 Fragment 时，将 [`FragmentStateAdapter`](https://developer.android.com/reference/kotlin/androidx/viewpager2/adapter/FragmentStateAdapter) 用于 `ViewPager2`。
     
     * 自定义动画，实现 `ViewPager2.PageTransformer` 接口并将其提供给 `ViewPager2` 对象。接口只会公开一个方法 `transformPage()`，方法中有一个 `position` 参数。
     
       > `position` 参数表示指定页面相对于屏幕中心的位置。 此参数是一个动态属性，会随着用户滚动浏览一系列页面而变化。当页面填满整个屏幕时，其位置值为 `0`。 当页面刚刚离开屏幕右侧时，其位置值为 `1`。如果用户在第一页和第二页之间滚动到一半，则第一页的位置为 -0.5，第二页的位置为 0.5。根据页面在屏幕上的位置，您可以使用 `setAlpha()`、`setTranslationX()` 或 `setScaleY()` 之类的方法设置页面属性，从而创建自定义滑动动画。
     
       * 支持嵌套的可滚动元素，必须对 `ViewPager2` 对象调用 [`requestDisallowInterceptTouchEvent()`](https://developer.android.com/reference/android/view/ViewGroup#requestDisallowInterceptTouchEvent(boolean))
     
    3. **预加载、懒加载、缓存**
    
       * 预加载
         由于使用的是RecyclerView，默认没有预加载，需要设置offScreenPageLimit
       
       * 懒加载
         如果没有设置offScreenPageLimit，就不需要考虑，只有有预加载我们才需要考虑懒加载。在onResume中添加变量来控制加载数据，预加载的时候不会走到onResume
       
       * 缓存
         需要学习RecyclerView的缓存机制
       
  
* 参考资料
  * [官网文档](https://developer.android.google.cn/guide/fragments)
  * [深入了解ViewPager2](https://juejin.cn/post/6844904020553760782)
  * [androidx中的Fragment懒加载方案](https://www.jianshu.com/p/a34eef0e3bc9)
  * [ViewPager2中的Fragment缓存、预取机制、懒加载实现方式](https://www.jianshu.com/p/1d95e729c571)
  * [Android总结 - Fragment](https://blog.csdn.net/Siobhan/article/details/51179833?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522162461185316780261988958%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=162461185316780261988958&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_v2~rank_v29-1-51179833.pc_v2_rank_blog_default&utm_term=Fragment&spm=1018.2226.3001.4450)
  * [【背上Jetpack之Fragment】你真的会用Fragment吗？Fragment常见问题以及androidx下Fragment的使用新姿势](https://juejin.cn/post/6844904079697657863)





## 3. 交互与布局



### include merge ViewStub

* 知识点
  * `<include/>`  ，在要添加可重复使用的组件的布局中，添加 `<include/>` 标记。（通过在 `<include/>` 标记中指定所添加布局的根视图的所有布局参数（任何 `android:layout_*` 属性），您还可以替换这些参数。不过，如果要使用 `<include>` 标记来替换布局属性，您必须同时替换 `android:layout_height` 和 `android:layout_width` 才能让其他布局属性生效。）
  * `<merge/>` 标记有助于消除视图层次结构中的冗余视图组
  * ` <ViewStub>` 视图加载延迟
* 参考资料
  * [Android抽象布局——include、merge 、ViewStub](http://blog.csdn.net/xyz_lmn/article/details/14524567)



### ViewSwitcher、TextSwitcher、ImageSwitcher

* 知识点
  * **ViewSwither** 继承 ViewAnimator，用来在两个View之间来回切换并可以设置不同的切换动画。ViewSwitcher 只能包含有两个子View，一次性只能显示其中一个。
  * **TextSwitcher** 继承ViewSwitcher，相当于指定了ViewSwitcher的子View只能是TextView。TextSwitcher 可以给屏幕上的Label加上切换动画。通过调用setText(CharSequence)把当前的text退出，显示下一个text并带有动画效果。
  * **ImageSwitcher**跟TextSwithcer类似，只是用于两个ImageView之间进行有动画的切换。添加子view(ImageView)的方式和设置切换动画都跟ViewSwitcher一样。当调用ImageSwitcher.setImage*方法时会进行两个ImageView的切换。
* 参考资料
  * [ViewSwitcher、TextSwitcher、ImageSwitcher 使用方法](http://blog.csdn.net/siobhan/article/details/51166380)



### ConstraintLayout

* 知识点
* 参考资料
  * [史上最全ConstraintLayout使用详解](https://mp.weixin.qq.com/s/V-jH0rlIUxgkjSbTV2WjrA)



### RecyclerView

* 知识点
  * RecyclerView 与ListView对比

    * 布局效果，RecyclerView 的布局效果丰富， 可以在 LayoutMananger 中设置
    * 封装了viewholder，Listview需要自己写ViewHolder缓存
    * RecyclerView的缓存机制有了加强，ListView是2级缓存，而RecyclerView实现了4级缓存
    * RecyclerView相对于ListView最大的加强是实现了局部刷新，这对于ListView需要刷新全部列表进步很多，特别适用于那些数据源经常发生改变的情况。
    * 已经封装好API来实现自己的动画效果，ListView并没有

  * RecyclerView核心组件 

    | 类名                        | 作用                                                     |
    | :-------------------------- | -------------------------------------------------------- |
    | Recycler.Adapter            | 处理数据集合并负责绑定视图                               |
    | RecyclerView.LayoutManager  | 负责Item视图的布局显示管理                               |
    | RecyclerView.ViewHolder     | 持有所有的用于绑定数据或者需要操作的View                 |
    | RecyclerView.ItemDecoration | 负责绘制Item附近的分割线                                 |
    | RecyclerView.ItemAnimator   | 为Item的一般操作添加动画效果，如：item进入、移动、删除等 |
    | RecyclerView.Recyler        | 负责处理Item View的缓存                                  |

    ![核心组件的关系](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt4iciasKiaPrFk69TMxh9DB0h55icAiciaoSlORib0U0FYWNRLZk5SZQQQxdMgZDW4D2NDThxD9aGVsE8UUw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

    * Recycler.Adapter

      1. Adapter 的常用复写的方法

         ```java
         public VH onCreateViewHolder(ViewGroup parent, int viewType) 创建Item视图，并返回相应的ViewHolder
         public void onBindViewHolder(VH holder, int position) 绑定数据到正确的Item视图上。
         public int getItemCount() 返回该Adapter所持有的Itme数量
         public int getItemViewType(int position) 用来获取当前项Item(position参数)是哪种类型的布局
         ```

      2. 通知数据集变化

         | Method                                                      | Description                                                  |
         | :---------------------------------------------------------- | :----------------------------------------------------------- |
         | `notifyItemChanged(int pos)`                                | Notify that item at the position has changed.                |
         | `notifyItemInserted(int pos)`                               | Notify that item reflected at the position has been newly inserted. |
         | `notifyItemRemoved(int pos)`                                | Notify that items previously located at the position have been removed from the data set. |
         | `notifyDataSetChanged()`                                    | Notify that the dataset has changed. Use only as last resort. |
         | `notifyItemRangeChanged(int positionStart, int itemCount)`  | Notify any registered observers that the `itemCount` items starting at position `positionStart` have changed. |
         | `notifyItemRangeInserted(int positionStart, int itemCount)` | Notify any registered observers that the currently reflected `itemCount` items starting at `positionStart` have been newly inserted. |
         | `notifyItemRangeRemoved(int positionStart, int itemCount)`  | Notify any registered observers that the `itemCount` items previously located at `positionStart` have been removed from the data set. |

      3. 大数据量变化 与 DiffUtil、AsyncListDiffer

      4. adapter订阅者模式

         

    * RecyclerView.LayoutManager

      1. LayoutManager的作用

         LayoutManager的职责是摆放Item的位置，并且负责决定何时回收和重用Item。

      2. 常用的LayoutManager
         - `LinearLayoutManager` 将各个项排列在一维列表中。将 `RecyclerView` 与 `LinearLayoutManager` 搭配使用可提供类似于旧版 `ListView` 布局的功能。
         - `GridLayoutManager` 将各个项排列在二维网格中，就像棋盘上的方格一样。将 `RecyclerView` 与 `GridLayoutManager` 搭配使用可提供类似于旧版 `GridView` 布局的功能。
         - `StaggeredGridLayoutManager` 将各个项排列在二维网格中，每一列都在前一列基础上稍微偏移，就像美国国旗中的星星一样。

      3. 如何自定义LayoutManager

         1）重写generateDefaultLayoutParams()

         2）重写 onLayoutChildren()

         3）重写 canScrollVertically() 或者 canScrollHorizontally()

         4）重写 scrollHorizontallyBy() 或者 scrollVerticallyBy()

         

      4. setLayoutManager源码里做了什么？

      

    * RecyclerView.ViewHolder

      1. ViewHolder的作用

      2. ViewHolder如何复用
  
         
  
    * RecyclerView.ItemDecoration

      1. ItemDecoration的作用，本质是一个Drawable

      2. 如何使用ItemDecoration

         ```java
       mDividerItemDecoration = new DividerItemDecoration(recyclerView.getContext(),
                      mLayoutManager.getOrientation());
       recyclerView.addItemDecoration(mDividerItemDecoration);
         ```

         
  
      3. 自定义ItemDecoration有哪些重写方法？
  
       
  
  * RecyclerView.ItemAnimator  动效
  
    1. 如何使用ItemAnimator  
  
       ```java
         //设置默认的动画模式
       recyclerView.setItemAnimator(new DefaultItemAnimator());
       ```

      2. ItemAnimator  中的几个重要方法
  
         
  
  * 点击&长按&选择|多选
  
    * Item的Touch事件
  
      1. `RecyclerView.OnItemTouchListener`
  
      ```
      recyclerView.addOnItemTouchListener(new RecyclerView.OnItemTouchListener() {
      
          @Override
          public void onTouchEvent(RecyclerView recycler, MotionEvent event) {
            // Handle on touch events here
          }
      
          @Override
        public boolean onInterceptTouchEvent(RecyclerView recycler, MotionEvent event) {
              return false;
        }
      
      ```
  
    });
      ```
    
      
    
    * Item的click事件 和 longClick事件
    
      1. Attaching Click Listeners with Decorators
    
         RecyclerView is to add a decorator class such as [this clever `ItemClickSupport` decorator](https://gist.github.com/nesquena/231e356f372f214c4fe6) and then implement the following code in your Activity or Fragment code:
    
      ```
         public class PostsFragment extends Fragment {
             // ...
         
             @Override
             public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
                 super.onViewCreated(view, savedInstanceState);
         
                 // Leveraging ItemClickSupport decorator to handle clicks on items in our recyclerView
                 ItemClickSupport.addTo(rvPosts).setOnItemClickListener(
                         new ItemClickSupport.OnItemClickListener() {
                             @Override
                             public void onItemClicked(RecyclerView recyclerView, int position, View v) {
                               // do stuff
                             }
                       }
                 );
           
             }
         
             // ...
         }
         ```
  
  
    ​     
  
      2. Simple Click Handler within ViewHolder
      
         ```
             // Used to cache the views within the item layout for fast access
             public class ViewHolder extends RecyclerView.ViewHolder implements View.OnClickListener {
                 public TextView tvName;
                 public TextView tvHometown;
                 private Context context;
         
                 public ViewHolder(Context context, View itemView) {
                     super(itemView);
                     this.tvName = (TextView) itemView.findViewById(R.id.tvName);
                     this.tvHometown = (TextView) itemView.findViewById(R.id.tvHometown);
                     // Store the context
                     this.context = context;
                     // Attach a click listener to the entire row view
                     itemView.setOnClickListener(this);
                 }
         
                 // Handles the row being being clicked
                 @Override
               public void onClick(View view) {
                     int position = getAdapterPosition(); // gets item position
                   if (position != RecyclerView.NO_POSITION) { // Check if an item was deleted, but the user clicked it before the UI removed it
                         User user = users.get(position);
                       // We can access the data within the views
                         Toast.makeText(context, tvName.getText(), Toast.LENGTH_SHORT).show();
                   }
               }
           }
       ```
  
  
  ​     
  
  * Item的选择和多选
  
    
  
* Header & Footer
  
  * RecyclerView实现添加HeaderView和FooterView的核心就是在Adapter里面的onCreateViewHolder根据viewType来判断是列表项还是HeaderView、FooterView来分别加载不同的布局文件
  
    
  
* RecyelerView滑动
  
  * 下拉刷新 & 加载更多
  
    1. 下拉刷新如何实现？
  
       
  
    2. 上拉加载更多如何实现？
  
       1） 使用  [EndlessRecyclerViewScrollListener.java](https://gist.github.com/nesquena/d09dc68ff07e845cc622) 
  
       
  
  * SnapHelper
  
    1. SnapHelper的作用
  
       在某些场景下，卡片列表滑动浏览[有的叫轮播图]，希望当滑动停止时可以将当前卡片停留在屏幕某个位置，比如停在左边，以吸引用户的焦点。那么可以使用RecyclerView + Snaphelper来实现
  
    2. SnapHelper是怎么让RecyclerView对齐的？
  
      3. LinearSnapHelper & PagerSnapHelper
  
    * 滑动冲突
  
      
  
  * 缓存（四级缓存）
  
    <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/62aee1745ef7435bb93293bc49aafbcf~tplv-k3u1fbpfcp-watermark.awebp" alt="img" style="zoom:80%;" />
  
    ![缓存获取流程](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/3/171d878fdd157587~tplv-t2oaga2asx-watermark.awebp)
  
    
  
  * RecyclerView性能优化
  
    * 数据优化
  
      1. 使用DiffUtil、AsyncListDiffer计算数据集的变化，实现局部更新
      2. 使用`recyclerView.setHasFixedSize(true);` ，如果items是固定且不会发生变化，为什么？
      
    * 布局优化
    
      性能优化的本质就是要减少这两个函数（onCreateViewHolder，onBindViewHolder）的调用时间和调用的次数。
  
  
  
* 源码分析
  
    * 知识点
      * 
    * 参考资料
      * [RecyclerView 源码分析(一) - RecyclerView的三大流程](https://www.jianshu.com/p/61fe3f3bb7ec)
      * [04.RecyclerView用法和源码深度解析.md](https://github.com/yangchong211/YCBlogs/blob/master/android/%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/04.RecyclerView%E7%94%A8%E6%B3%95%E5%92%8C%E6%BA%90%E7%A0%81%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90.md)
  
* 参考资料
  * [RecyclerView - Android官网文档](https://developer.android.google.cn/guide/topics/ui/layout/recyclerview?hl=zh_cn)
  * [[CodePath](Using the RecyclerView (Android 5.0))](http://guides.codepath.com/android/Using-the-RecyclerView)
  * [[CodePath\] Heterogenous Layouts inside RecyclerView (Android 5.0)](http://guides.codepath.com/android/Heterogenous-Layouts-inside-RecyclerView)
  * [Android 优雅的为RecyclerView添加HeaderView和FooterView](http://blog.csdn.net/lmj623565791/article/details/51854533)
  * [[CodePath\] Implementing Pull to Refresh Guide](http://guides.codepath.com/android/Implementing-Pull-to-Refresh-Guide)
  * [[CodePath\]Endless Scrolling with AdapterViews and RecyclerView (Infinite pagination)](http://guides.codepath.com/android/Endless-Scrolling-with-AdapterViews-and-RecyclerView)
  * [你必须了解的RecyclerView的五大开源项目-解决上拉加载、下拉刷新和添加Header、Footer等问题](http://blog.csdn.net/mynameishuangshuai/article/details/51153978)
  * [深入理解 RecyclerView 的缓存机制](https://juejin.cn/post/6844904146684870669)
  * [RecyclerView问题汇总](https://juejin.cn/post/6844903837724213256)
  * [看完这篇，面试RecyclerView的时候再也不怕了](https://mp.weixin.qq.com/s/auphzaQF6_wJx6dGFY6niA)





### WebView

* 知识点
  * 在WebView中使用JavaScript，默认是关闭的；JS和Android之间相互调用，WebView使用`addJavascriptInterface()` 来给JS添加接口；但是出于安全原因，除非全部的代码都是自己所写，不然建议使用默认的浏览器。
  * 网页导航
    * 如果想要在应用中打开链接调整，则需要给WebView设置一个WebViewClient，使用WebViewClient中的` shouldOverrideUrlLoading` 可以响应网页中的超链接，可以对请求进行拦截。使用`goBack()` 和`goForward()`可以向后或向前浏览历史记录。
  * WebViewClient 和 WebChromeClient 
    * WebViewClient就是帮助WebView处理各种通知、请求事件的。
    * WebChromeClient是辅助WebView处理[JavaScript](https://link.juejin.cn/?target=http%3A%2F%2Flib.csdn.net%2Fbase%2Fjavascript)的对话框，网站图标，网站title，加载进度等 。
      方法中的代码都是由 [Android](https://link.juejin.cn/?target=http%3A%2F%2Flib.csdn.net%2Fbase%2Fandroid)端自己处理。
* 参考资料
  * [Working with the WebView - CodePath - 使用篇](http://guides.codepath.com/android/Working-with-the-WebView) 
  * [WebView你真的熟悉吗？看了才知道](http://www.jianshu.com/p/d2f5ae6b4927)





### ImageView

* 知识点
  * 属性

    * adjustViewBounds，保持原图的长宽比，需要设置maxWidth/maxHeight 或者长宽中使用wrap_content

  * Scale Types

    * center：保持原图的大小，显示在ImageView的中心。当原图的size大于ImageView的size，超过部分裁剪处理。

    * centerCrop：以填满整个ImageView为目的，将原图的中心对准ImageView的中心，等比例放大缩小原图，直到填满ImageView为止（指的是ImageView的宽和高都要填满），放大或缩小后的原图超过ImageView的部分作裁剪处理。（去除Padding）
    * centerInside：以原图完全显示为目的，将图片的内容完整居中显示，通过按比例放大缩小原图的size宽(高)等于或小于ImageView的宽(高)。如果原图的size本身就小于ImageView的size，则原图的size不作任何处理，居中显示在ImageView。（当原图长宽都小于ImageView的时候，不进行任何处理，直接居中显示，当原图长宽大于ImageView的时候，效果如同fitCenter）
    * matrix：不改变原图的大小，从ImageView的左上角开始绘制原图，原图超过ImageView的部分作裁剪处理。
    * fitCenter：把原图按比例扩大或缩小长和宽，保证原图整体都在ImageView中，并且原图中的长或者宽肯定有一个等于ImageView的长或者宽，居中显示。
    * fitStart：把原图按比例扩大或缩小长和宽，保证原图整体都在ImageView中，并且原图中的长或者宽肯定有一个等于ImageView的长或者宽，显示在ImageView的上/前部分位置
    * fitEnd：把原图按比例扩大或缩小长和宽，保证原图整体都在ImageView中，并且原图中的长或者宽肯定有一个等于ImageView的长或者宽，显示在ImageView的下/后部分位置
    * fitXY：把原图按照指定的大小在View中显示，拉伸显示图片，不保持原比例，填满ImageView.

    ![](https://img-blog.csdn.net/20160503100939448)

    

  * ImageView和Bitmap
    
    * 
* SVG图片
  
* 参考资料
  
  * [Working with the ImageView](https://github.com/codepath/android_guides/wiki/Working-with-the-ImageView)



### Bitmap

* 知识点
  * Bitmap 与 density、dpi直接的关系

    <img src="https://developer.android.google.cn/images/screens_support/devices-density_2x.png?hl=zh-cn" alt="img" style="zoom:80%;" />

    * 资源目录下的匹配优先级
      * 如果在最匹配的目录没有找到对应图片，就会向更高密度的目录查找，直到没有更高密度的目录
      * 如果一直往高密度目录均没有查找，Android就会查找drawable-nodpi目录。drawable-nodpi目录中的资源适用于所有密度的设备，不管当前屏幕的密度如何，系统都不会缩放此目录中的资源。
      * 如果在drawable-nodpi目录也没有查找到，系统就会向比最匹配目录密度低的目录依次查找，直到没有更低密度的目录。例如，最匹配目录是xxhdpi，更高密度的目录和nodpi目录查找不到后，就会依次查找drawable-xhdp、drawable-hdpi、drawable-mdpi、drawable-ldpi。
        

  * Bitmap内存占用

    - 内存存放位置

      - 8.0之前的Bitmap像素数据基本存储在Java heap
      - 8.0之后的 Bitmap像素数据基本存储在native heap

    - 大小受影响：Bitmap的像素存储格式

      | 格式      | 意义                                                         | 单个像素占用字节数 |
      | --------- | ------------------------------------------------------------ | ------------------ |
      | ALPHA_8   | 表示8位Alpha位图,即A=8,一个像素点占用1个字节,它没有颜色,只有透明度 | 1                  |
      | ARGB_4444 | 表示16位ARGB位图，即A=4,R=4,G=4,B=4,一个像素点占4+4+4+4=16位 | 2                  |
      | ARGB_8888 | 表示32位ARGB位图，即A=8,R=8,G=8,B=8,一个像素点占8+8+8+8=32位 | 4                  |
      | RGB_565   | 表示16位RGB位图，即R=5,G=6,B=5,它没有透明度,一个像素点占5+6+5=16位 | 2                  |

    

  * Bitmap加载、压缩、拉伸裁剪、保存

    * 加载

      * decodeResource()和decodeFile()

        decodeFile()用于读取SD卡上的图，得到的是图片的原始尺寸； decodeResource()用于读取Res、Raw等资源，得到的是图片的原始尺寸 * 缩放系数。

    * 压缩

      * 质量压缩，Bitmap.compress

      * 缩略图和计算inSampleSize

        需要先设置BitmapFactory.Options的inJustDecodeBounds为true，这样的Bitmap可以借助decodeFile方法把高和宽存放到Bitmap.Options中，但是内存占用为空（不会真正的加载图片）。

        ```java
         /* 获取缩略图 * 支持自动旋转 * 某些型号的手机相机图片是反的，可以根据exif信息实现自动纠正 * @return */
        public static Bitmap $thumbnail(String path,
                int maxWidth, int maxHeight, boolean autoRotate) {
            int angle = 0;
            if (autoRotate) {
                angle = ImageLess.$exifRotateAngle(path);
            }
            BitmapFactory.Options options = new BitmapFactory.Options();
            options.inJustDecodeBounds = true;
            Bitmap bitmap = BitmapFactory.decodeFile(path, options);
            options.inJustDecodeBounds = false;
            int sampleSize = $sampleSize(options, maxWidth, maxHeight);
            options.inSampleSize = sampleSize;
            options.inPreferredConfig = Bitmap.Config.RGB_565;
            options.inPurgeable = true;
            options.inInputShareable = true;
            if (bitmap != null && !bitmap.isRecycled()) {
                bitmap.recycle();
            }
            bitmap = BitmapFactory.decodeFile(path, options);
            if (autoRotate && angle != 0) {
                bitmap = $rotate(bitmap, angle);
            }
            return bitmap;
            }
        
        ```

        

        

    * 拉伸裁剪

      * Matrix、Canvas、Bitmap.createBitmap

    * 保存

      ```java
      public static String $save(Bitmap bitmap,
              Bitmap.CompressFormat format, int quality, File destFile) {
          try {
              FileOutputStream out = new FileOutputStream(destFile);
              if (bitmap.compress(format, quality, out)) {
                  out.flush();
                  out.close();
              }
              if (bitmap != null && !bitmap.isRecycled()) {
                  bitmap.recycle();
              }
              return destFile.getAbsolutePath();
          } catch (FileNotFoundException e) {
              e.printStackTrace();
          } catch (IOException e) {
              e.printStackTrace();
          }
          return null;
          }
      
      ```

      

  * Bitmap大图加载 - BitmapRegionDecoder

    1. 使用

       （1）创建BitmapRegionDecoder
               使用区域解码，那么我们首先需要创建一个BitmapRegionDecoder对象。只需要调用newInstance方法，传入一个InputStream和一个boolean值。如下所示：

       ```java
       mDecoder = BitmapRegionDecoder.newInstance(is, false);
       ```

       （2）解码Bitmap
               调用decodeRegion方法解码Bitmap，需要传入一块区域，以及参数，代码如下：

       ```java 
       Bitmap bitmap = mDecoder.decodeRegion(mRect, mDecodeOptions);
       ```

       

  * ThumbnailUtils类

    ThumbnailUtils是系统提供的一个专门生成缩略图的方法

  * Bitmap缓存和复用：

    * LRUCache、DiskLruCache

    * inBitmap

      Android 3.0（API 级别 11）引入了 `BitmapFactory.Options.inBitmap` 字段。如果设置了此选项，那么采用 `Options` 对象的解码方法会在加载内容时尝试重复使用现有位图。这意味着位图的内存得到了重复使用，从而提高了性能，同时移除了内存分配和取消分配。不过，`inBitmap` 的使用方式存在某些限制。特别是在 Android 4.4（API 级别 19）之前，系统仅支持大小相同的位图。

  * 图片加载框架：  [Glide](#Glide)、Fresco
* 参考资料
  * 《Android开发艺术探索》
  * [Android Bitmap 面面观](https://juejin.cn/post/6844903433313452040)
  * [Android 开发绕不过的坑：你的 Bitmap 究竟占多大内存？](https://www.cnblogs.com/krislight1105/p/5203277.html)
  * [Android Bitmap变迁与原理解析（4.x-8.x）](https://juejin.cn/post/6844903608887017485)
  * [Android 高清加载巨图方案 拒绝压缩图片](https://blog.csdn.net/lmj623565791/article/details/49300989)



### Drawable

* 知识点
  * BitmapDrawable（位图图形文件（`.png`、`.jpg` 或 `.gif`））
  * ShapeDrawable
  * StateListDrawable
  * LayerListDrawable
  * NinePatchDrawable
  * VectorDrawable
* 参考资料
  * [Drawables -  CodePath - 使用篇](https://guides.codepath.com/android/Drawables)
  * [可绘制对象资源_官网](https://developer.android.com/guide/topics/resources/drawable-resource#Transition)
  * 《Android开发艺术探索》



### 窗口 window 使用

* 知识点

  * 显示、移出和更新窗口

    * 主要是调用WindowManager提供的三个方法

      ```java
      public interface ViewManager{
          //'向窗口添加视图'
          public void addView(View view, ViewGroup.LayoutParams params);
          //'更新窗口中视图'
          public void updateViewLayout(View view, ViewGroup.LayoutParams params);
          //'移除窗口中视图'
          public void removeView(View view);
      }
      
      ```

  * WindowManager.LayoutParams

    * type

      Window有一个Z-Order属性，Z-Order越大，window越靠近用户，也就显示越高，高度高的window会覆盖高度低的window。window的type属性就是Z-Order的值，我们可以给window的type属性赋值来决定window的高度。

      | Window 类型 | 含义                                                         | Window 层级 |
      | :---------- | :----------------------------------------------------------- | :---------- |
      | 应用 Window | 对应着一个 Activity                                          | 1-99        |
      | 子 Window   | 不能单独存在，需要附属在父 Window 中（比如 Dialog 就是子 Window） | 1000-1999   |
      | 系统 Window | 需要声明权限才能创建 Window ，比如 Toast 、状态栏、悬浮窗    | 2000-2999   |

      

    * flag

      flag控制的范围包括了：各种情景下的显示逻辑（锁屏，游戏等）还有触控事件的处理逻辑。控制显示确实是他的很大部分功能，但是并不是全部。以下是常用的flag：

      ```java
      // 当 Window 可见时允许锁屏
      public static final int FLAG_ALLOW_LOCK_WHILE_SCREEN_ON = 0x00000001;
      
      // Window 后面的内容都变暗
      public static final int FLAG_DIM_BEHIND = 0x00000002;
      
      // Window 不能获得输入焦点，即不接受任何按键或按钮事件，例如该 Window 上 有 EditView，点击 EditView 是 不会弹出软键盘的
      // Window 范围外的事件依旧为原窗口处理；例如点击该窗口外的view，依然会有响应。另外只要设置了此Flag，都将会启用FLAG_NOT_TOUCH_MODAL
      public static final int FLAG_NOT_FOCUSABLE = 0x00000008;
      
      // 设置了该 Flag,将 Window 之外的按键事件发送给后面的 Window 处理, 而自己只会处理 Window 区域内的触摸事件
      // Window 之外的 view 也是可以响应 touch 事件。
      public static final int FLAG_NOT_TOUCH_MODAL  = 0x00000020;
      
      // 设置了该Flag，表示该 Window 将不会接受任何 touch 事件，例如点击该 Window 不会有响应，只会传给下面有聚焦的窗口。
      public static final int FLAG_NOT_TOUCHABLE      = 0x00000010;
      
      // 只要 Window 可见时屏幕就会一直亮着
      public static final int FLAG_KEEP_SCREEN_ON     = 0x00000080;
      
      // 允许 Window 占满整个屏幕
      public static final int FLAG_LAYOUT_IN_SCREEN   = 0x00000100;
      
      // 允许 Window 超过屏幕之外
      public static final int FLAG_LAYOUT_NO_LIMITS   = 0x00000200;
      
      // 全屏显示，隐藏所有的 Window 装饰，比如在游戏、播放器中的全屏显示
      public static final int FLAG_FULLSCREEN      = 0x00000400;
      
      // 表示比FLAG_FULLSCREEN低一级，会显示状态栏
      public static final int FLAG_FORCE_NOT_FULLSCREEN   = 0x00000800;
      
      // 当用户的脸贴近屏幕时（比如打电话），不会去响应此事件
      public static final int FLAG_IGNORE_CHEEK_PRESSES    = 0x00008000;
      
      // 则当按键动作发生在 Window 之外时，将接收到一个MotionEvent.ACTION_OUTSIDE事件。
      public static final int FLAG_WATCH_OUTSIDE_TOUCH = 0x00040000;
      
      @Deprecated
      // 窗口可以在锁屏的 Window 之上显示, 使用 Activity#setShowWhenLocked(boolean) 方法代替
      public static final int FLAG_SHOW_WHEN_LOCKED = 0x00080000;
      
      // 表示负责绘制系统栏背景。如果设置，系统栏将以透明背景绘制，
      // 此 Window 中的相应区域将填充 Window＃getStatusBarColor（）和 Window＃getNavigationBarColor（）中指定的颜色。
      public static final int FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS = 0x80000000;
      
      // 表示要求系统壁纸显示在该 Window 后面，Window 表面必须是半透明的，才能真正看到它背后的壁纸
      public static final int FLAG_SHOW_WALLPAPER = 0x00100000;
      
      ```

    * dimAmount

      这个参数是要和`WindowManager.LayoutParams.FLAG_DIM_BEHIND`这个flag属性一起使用，dimAmount的取值在0.0f~1.0f之间，取值越大背景的变暗程度越高，默认取值1.0f。

    * width、height

      和View中的width、height一样的理解，就是控制窗口视图的大小

    * gravity

      窗口的对齐方式，一般在创建窗口的时候，都会设置gravity为左上角对齐，也就是`Gravity.LEFT | Gravity.TOP`，因为窗口的坐标设置，是基于gravity来进行计算的，设置gravity左上角，刚好是和系统的坐标相对应，方便计算。

    * x、y

      x和y用于控制窗口的坐标位置，如果有设置gravity的话，x和y设置的就是在gravity这个基础上的一个偏移量。不设置gravity的话，x和y就是一个绝对坐标。

    * windowAnimations

      windowAnimations控制的是窗口出现和消失的动画效果，设置的是要系统自带的动画效果（android.R.style之下的动画效果），因为窗口管理器是不能访问应用资源的。

    * format

      format可以理解为最后窗口生成的位图是什么格式，默认背景是黑色的。一般我们都设置为PixelFormat.RGBA_8888，这样我们的窗口就会有一个透明的背景。

    * alpha

      window的透明度

  * 设置悬浮窗点击事件

    为浮窗设置点击事件等价于为浮窗视图设置点击事件。

  * 拖拽窗口

    调用updateViewLayout更新Layoutparam的x，y即可

  * 浮框自动贴边

    把贴边理解成一个水平位移动画。在松手时求出动画起点和终点横坐标，利用动画值不断更新浮窗位置

  * 监听浮窗界外点击事件

    * window添加FLAG_WATCH_OUTSIDE_TOUCH的flag，在 onTouch中监听ACTION_OUTSIDE事件。
    
  * 浮窗的几种实现方式

    * 应用内悬浮窗
    * addContentView实现
    * 应用外悬浮窗(有局限性)
    * 需要申请权限 并且在Service中添加
    * 无障碍悬浮窗

* 参考资料
  
  * [悬浮窗的一种实现 | Android悬浮窗Window应用](https://juejin.cn/post/6844904038153093127#heading-7)
  * [Android全面解析之Window机制](https://juejin.cn/post/6888688477714841608)
  * [Android悬浮窗看这篇就够了](https://juejin.cn/post/6951608145537925128#heading-6)
  * [像360悬浮窗那样，用WindowManager实现炫酷的悬浮迷你音乐盒（上）](http://www.jianshu.com/p/95ceb0a2ed27)



### Android View事件体系

* 知识点

  * **View坐标系**

    1. View提供的方法

       * getTop：获取到的是view自身的顶边到其父布局顶边的距离
       * getLeft：获取到的是view自身的左边到其父布局左边的距离
       * getRight：获取到的是view自身的右边到其父布局左边的距离
       * getBottom：获取到的是view自身的底边到其父布局顶边的距离
       * getX()、getY()：x，y是View左上角的坐标（相对于父容器的坐标）  x = left + translationX
       * getTranslationX（）、getTranslationY（）：是View左上角相对于父容器的偏移量
       
       >  View在平移的过程中，top和left表示的是原始左上角的位置信息，其值不会发生改变，此时改变的是x、y、translationX和translationY这四个参数。
       
       * getLocationInWindow（）：获取控件 相对 窗口Window 的位置
       * getLocationOnScreen（）：获得 View 相对 屏幕 的绝对坐标
       * getGlobalVisibleRect（）：View可见部分 相对于 屏幕的坐标。
       * getLocalVisibleRect（）：View可见部分 相对于 自身View位置左上角的坐标。
    
    

    2. MotionEvent中的方法
       * getX()：获取点击事件相对控件左边的x轴坐标，即点击事件距离控件左边的距离
       * getY()：获取点击事件相对控件顶边的y轴坐标，即点击事件距离控件顶边的距离
       * getRawX()：获取点击事件相对整个屏幕左边的x轴坐标，即点击事件距离整个屏幕左边的距离
       * getRawY()：获取点击事件相对整个屏幕顶边的y轴坐标，即点击事件距离整个屏幕顶边的距离
    
    <img src="https://img-blog.csdn.net/20160416152117873" style="zoom: 50%;" />

    3. TouchSlop

       TouchSlop是系统所能识别出的被认为是滑动的最小距离，换个说法，当手指在屏幕上滑动时，如果两次滑动之间的距离小于这个 常量，那么系统就不认为你是在进行滑动操作。

       获取方法：

       ```java
     ViewConfiguration.get(getContext()).getScaledTouchSlop();
       ```
    
       

    4. 获取View宽高的时机

       * ViewTreeObserver  - addOnGlobalLayoutListener
     * ViewTreeObserver  -  addOnPreDrawListener
       * view.post()
       * onWindowFocusChanged
       
       > 参考：[Android--获取View的宽高的几种方法](https://blog.csdn.net/HardWorkingAnt/article/details/77278811)
    
    5. 常用的工具类

       * VelocityTracker

         速度追踪，用于追踪手指在滑动过程中的速度，包括水平和竖直方向的速度。

         ```kotlin
           private const val DEBUG_TAG = "Velocity"
         
             class MainActivity : Activity() {
                 private var mVelocityTracker: VelocityTracker? = null
         
                 override fun onTouchEvent(event: MotionEvent): Boolean {
         
                     when (event.actionMasked) {
                         MotionEvent.ACTION_DOWN -> {
                             // Reset the velocity tracker back to its initial state.
                             mVelocityTracker?.clear()
                             // If necessary retrieve a new VelocityTracker object to watch the
                             // velocity of a motion.
                             mVelocityTracker = mVelocityTracker ?: VelocityTracker.obtain()
                             // Add a user's movement to the tracker.
                             mVelocityTracker?.addMovement(event)
                         }
                         MotionEvent.ACTION_MOVE -> {
                             mVelocityTracker?.apply {
                                 val pointerId: Int = event.getPointerId(event.actionIndex)
                                 addMovement(event)
                                 // When you want to determine the velocity, call
                                 // computeCurrentVelocity(). Then call getXVelocity()
                                 // and getYVelocity() to retrieve the velocity for each pointer ID.
                                 computeCurrentVelocity(1000)
                                 // Log velocity of pixels per second
                                 // Best practice to use VelocityTrackerCompat where possible.
                                 Log.d("", "X velocity: ${getXVelocity(pointerId)}")
                                 Log.d("", "Y velocity: ${getYVelocity(pointerId)}")
                             }
                         }
                         MotionEvent.ACTION_UP, MotionEvent.ACTION_CANCEL -> {
                             // Return a VelocityTracker object back to be re-used by others.
                             mVelocityTracker?.recycle()
                             mVelocityTracker = null
                         }
                     }
                     return true
                 }
             }
             
         ```
    
         

       * GestureDetector

         手势检测，用于辅助检测用户的单击、滑动、长按、双击等行为。如果您只想处理几个手势，可以扩展 `GestureDetector.SimpleOnGestureListener`，而无需实现 `GestureDetector.OnGestureListener` 接口。

         ```kotlin
           private const val DEBUG_TAG = "Gestures"
         
             class MainActivity :
                     Activity(),
                     GestureDetector.OnGestureListener,
                     GestureDetector.OnDoubleTapListener {
         
                 private lateinit var mDetector: GestureDetectorCompat
         
                 // Called when the activity is first created.
                 public override fun onCreate(savedInstanceState: Bundle?) {
                     super.onCreate(savedInstanceState)
                     setContentView(R.layout.activity_main)
                     // Instantiate the gesture detector with the
                     // application context and an implementation of
                     // GestureDetector.OnGestureListener
                     mDetector = GestureDetectorCompat(this, this)
                     // Set the gesture detector as the double tap
                     // listener.
                     mDetector.setOnDoubleTapListener(this)
                 }
         
                 override fun onTouchEvent(event: MotionEvent): Boolean {
                     return if (mDetector.onTouchEvent(event)) {
                         true
                     } else {
                         super.onTouchEvent(event)
                     }
                 }
         
                 override fun onDown(event: MotionEvent): Boolean {
                     Log.d(DEBUG_TAG, "onDown: $event")
                     return true
                 }
         
                 override fun onFling(
                         event1: MotionEvent,
                         event2: MotionEvent,
                         velocityX: Float,
                         velocityY: Float
                 ): Boolean {
                     Log.d(DEBUG_TAG, "onFling: $event1 $event2")
                     return true
                 }
         
                 override fun onLongPress(event: MotionEvent) {
                     Log.d(DEBUG_TAG, "onLongPress: $event")
                 }
         
                 override fun onScroll(
                         event1: MotionEvent,
                         event2: MotionEvent,
                         distanceX: Float,
                         distanceY: Float
                 ): Boolean {
                     Log.d(DEBUG_TAG, "onScroll: $event1 $event2")
                     return true
                 }
         
                 override fun onShowPress(event: MotionEvent) {
                     Log.d(DEBUG_TAG, "onShowPress: $event")
                 }
         
                 override fun onSingleTapUp(event: MotionEvent): Boolean {
                     Log.d(DEBUG_TAG, "onSingleTapUp: $event")
                     return true
                 }
         
                 override fun onDoubleTap(event: MotionEvent): Boolean {
                     Log.d(DEBUG_TAG, "onDoubleTap: $event")
                     return true
                 }
         
                 override fun onDoubleTapEvent(event: MotionEvent): Boolean {
                     Log.d(DEBUG_TAG, "onDoubleTapEvent: $event")
                     return true
                 }
         
                 override fun onSingleTapConfirmed(event: MotionEvent): Boolean {
                     Log.d(DEBUG_TAG, "onSingleTapConfirmed: $event")
                     return true
                 }
         
             }
         ```
    
         

       * Scroller

         弹性滑动对象，用于实现View 的弹性滑动。Scroller本身无法让View弹性滑动，它需要和View的computeScroll方法配合使用才能共同完成这个功能。

    

  * **View的滑动**

    * 使用scrollTo/scrollBy（相对滑动）
      1. View内部有两个属性mScrollX和mScrollY，scrollTo()中mScrollX的值等于view左边缘和view内容左边缘在水平方向的距离，并且当view的左边缘在view的内容左边缘右边时，mScrollX为正，反之为负；同理mScrollY等于view上边缘和view内容上边缘在竖直方向的距离，并且当view的上边缘在view的内容上边缘下边时，mScrollY为正，反之为负。当View没有使用scrollTo()和scrollBy()进行滑动的时候，mScrollX和mScrollY默认等于零，也就是view的左边缘与内容左边缘重合。
      2. scrollTo/scrollBy只能改变View内容的位置而不能改变View在布局中的位置。
      3. 适用场景：操作简单，适合对View内容的滑动
    * 使用动画
      1. 适用场景：操作简单，主要适用于没有交互的View和实现复杂的动画效果
    * 改变布局参数
      1. 适用场景：操作稍微复杂，适用于有交互的View

    

  * **View事件传递机制**

    * WindowInputEventReceiver接收到Input事件后的如何传到到Activity的？
    
      ![](https://upload-images.jianshu.io/upload_images/10866057-4b5c124f3234dedb.png?imageMogr2/auto-orient/strip|imageView2/2/w/891/format/webp)
    
    
    
    * 从Activity往下传递事件的流程？
    
      ![](https://upload-images.jianshu.io/upload_images/10866057-258980d5ede08a3a.png?imageMogr2/auto-orient/strip|imageView2/2/w/1012/format/webp)
    
    * 事件传递机制的总结：
      1. **事件序列**是指按下到抬起之间发生的一系列事件.
      2. 默认一个事件序列只能被一个View拦截并消耗. (例外：采用非常规,在onTouchEvent强行传递给其他View. 不推荐)
      3. 某个View一旦决定拦截，那么这个事件序列只能由它自己处理（如果事件序列能够传递给它的话）， 并且它的`onInterceptTouchEvent()`不会再被调用。
      4. 某个View一旦开始处理事件，如果View不消耗`ACTION_DOWN`事件（onTouchEvent返回了false）, 那么同一个事件序列都不会再交给它来处理，并且事件会重新传递到父元素的`onTouchEvent()`再次调用方法。
      5. 如果View不消耗除`ACTION_DOWN`以外的其他事件，那么这个点击事件会消失，而此时父元素的`onTouchEvent()`不会被调用，并且当前View可以持续收到后续的事件，最终这些消失的事件会传递到activity处理。
      6. `ViewGroup`默认不拦截任何事件, 源码中ViewGroup的`onInterceptTouchEvent()`默认返回false
      7. `View`没有`onInterceptTouchEvent()`, 因为它没有子View,所以直接调用`onTouchEvent()`
      8. `View`的`onTouchEvent`默认都会消耗事件返回true，除非它不可点击的(需要`clickable`和`longClickable`同时为false)。View的`longClickable`默认都为false，而`clickable`需要区分控件, 如`Button`默认为true, `TextView`默认为false.
      9. `View`的`enable`属性不影响`onTouchEvent`的默认返回值，哪怕一个View是`disable`状态. 只要它的`clickable`或者`longClickable`有一个为true. 那么它的`onTouchEvent()`就返回true
      10. `onClick`会发生的前提是当前View为可点击，并且他收到了down和up事件
      11. 事件传递的过程是由外向内的，通过`requestDisallowInterceptTouchEvent()`可以在子元素中干预父元素的事件分发过程，但是`ACTION_DOWN`事件除外.
    
    
    
  * **View的滑动冲突**

    * 常见的滑动冲突场景

      * 场景1：外部滑动方向和内部滑动方向不一致；
      * 场景2：外部滑动方向和内部滑动方向一致；
      * 场景3：上面两种情况的嵌套。

    * 滑动冲突的处理规则

      * 横竖方向判断的方法：1）可以依据滑动路径和水平方向所形成的夹角  2）也可以依据水平方向和竖直方向上的距离差来判断  3）某些特殊时候还可以依据水平和竖直方向的速度差来做判断

    * 滑动冲突的解决方式

      * 外部拦截法

        > 是指点击事件都是需要先经过父容器的拦截处理, 如果父容器需要此事件就拦截,不需要就下放. 外部拦截需要重写父容器的onInterceptTouchEvent方法
    
        大概的处理流程：

        - `ACTION_DOWN`这个事件,父容器必须返回false, 即不拦截`ACTION_DOWN`事件, 因为一旦父容器拦截了这个事件, 那么后续的`ACTION_MOVE`,`ACTION_UP`事件都会交由父容器来处理了. 这个时候这个**事件序列**剩余部分无法传递给子元素了.
        - `ACTION_MOVE`这个事件,就可以根据实际的需求来决定是否需要拦截. 如果需要拦截就返回true.否则false.
        - `ACTION_UP`这个事件必须返回false, 因为`ACTION_UP`事件本身没有太多意义.

      * 内部拦截法

        > 是指父容器不拦截任何事件, 所有的事件都需要传递给子元素, 如果子元素需要此事件就直接消费. 否则就交由父容器进行处理, 由于这种方法和Android中的事件分发机制不一致, 需要配合requestDisallowInterceptTouchEvent()方式才能正常工作. 需要重写子元素的dispatchTouchEvent

        这种拦截法的使用规则:
    
        子View中的`dispatchTouchEvent()`进行复写.

        - `ACTION_DOWN`事件中: 让父容器拒绝拦截所有事件, 调用`parent.requestDisallowInterceptTouchEvent(true)`
        - `ACTION_MOVE`事件中: 进行条件的拦截判断, 如果在某一种场景需要拦截,那么就调用方法允许父容器拦截事件.
        - `return` 时, 调用`super.dispatchTouchEvent(event)`

        父容器的`onInterceptTouchEvent()`进行`ACTION_DOWN`返回false, 其余都是返回true的复写.
    
        说明一点, 为什么父容器不连`ACTION_DOWN`一并的用true复写. 因为`ACTION_DOWN`这个事件是不受`INTERCEPT_FLAG`这个标记影响的的, 就是不管拦截标记是否是何值, 按下事件必然会执行, 所以如果这里返回true, 那么就代表着, 这个事件序列的后续部分将由父容器进行处理, 而子容器无法收到这个事件.
    
    

* 参考资料

  * [Android：你知道该如何正确获取View坐标位置的方法吗？](https://blog.csdn.net/carson_ho/article/details/103342511)

  * [那些你应该知道却不一定知道的——View坐标分析汇总](https://blog.csdn.net/mr_immortalz/article/details/51168278)

  * [安卓自定义View进阶-多点触控详解](http://www.gcssloop.com/customview/multi-touch.html)
  
  * [可能是讲解Android事件分发最好的文章](https://cloud.tencent.com/developer/article/1179381)
  
  * [Android 事件分发流程](https://www.jianshu.com/p/40e083ac6111)
  
  * 《Android开发艺术探索》
  
    

### Android View的工作原理

* 知识点

  * **ViewRootImpl和DecorView**

    View的三大流程均是通过ViewRoot来完成的。在ActivityThread中，当Activity对象被创建完毕后，会将DecorView添加到Window中，同时会创建ViewRootImpl对象，并将ViewRootImpl对象和DecorView建立关联，这个过程可参看如下源码：

    ```java
        root = new ViewRootImpl(view.getContext(), display);
        root.setView(view, wparams, panelParentView);
    ```

    

    View的绘制流程是从ViewRoot的performTraversals方法开始的，它经过measure、layout和draw三个过程才能最终将一个View绘制出来，其中measure用来测量View的宽和高，layout用来确定View在父容器中的放置位置，而draw则负责将View绘制在屏幕上。

    <img src="https://img.kancloud.cn/65/a5/65a57064e2c94c9730c6a2db54767dfa_836x611.png" alt="img" style="zoom: 80%;" />

    performTraversals会依次调用performMeasure、performLayout和performDraw三个方法，这三个方法分别完成顶级View的measure、layout和draw这三大流程，其中在performMeasure中会调用measure方法，在measure方法中又会调用onMeasure方法，在onMeasure方法中则会对所有的子元素进行measure过程，这个时候measure流程就从父容器传递到子元素中了，这样就完成了一次measure过程。接着子元素会重复父容器的measure过程，如此反复就完成了整个View树的遍历。同理，performLayout和performDraw的传递流程和performMeasure是类似的，唯一不同的是，performDraw的传递过程是在draw方法中通过dispatchDraw来实现的，不过这并没有本质区别。

  * **理解MeasureSpec**

    在测量过程中，系统会将View的LayoutParams根据父容器所施加的规则转换成对应的MeasureSpec，然后再根据这个measureSpec来测量出View的宽/高。

    * MeasureSpec

      MeasureSpec代表一个32位int值，高2位代表SpecMode，低30位代表SpecSize, SpecMode是指测量模式，而SpecSize是指在某种测量模式下的规格大小。MeasureSpec内部的一些常量的定义如下：

      ```java
          private static final int MODE_SHIFT = 30;
          private static final int MODE_MASK = 0x3 << MODE_SHIFT;
          public static final int UNSPECIFIED = 0 << MODE_SHIFT;
          public static final int EXACTLY = 1 << MODE_SHIFT;
          public static final int AT_MOST = 2 << MODE_SHIFT;
      
          public static int makeMeasureSpec(int size, int mode) {
              if (sUseBrokenMakeMeasureSpec) {
                  return size + mode;
              } else {
                  return (size & ～MODE_MASK) | (mode & MODE_MASK);
              }
          }
          public static int getMode(int measureSpec) {
              return (measureSpec & MODE_MASK);
          }
      
          public static int getSize(int measureSpec) {
              return (measureSpec & ～MODE_MASK);
          }
      ```

      MeasureSpec通过将SpecMode和SpecSize打包成一个int值来避免过多的对象内存分配，为了方便操作，其提供了打包和解包方法。SpecMode和SpecSize也是一个int值，一组SpecMode和SpecSize可以打包为一个MeasureSpec，而一个MeasureSpec可以通过解包的形式来得出其原始的SpecMode和SpecSize，需要注意的是这里提到的MeasureSpec是指MeasureSpec所代表的int值，而并非MeasureSpec本身。

      

      SpecMode有三类，每一类都表示特殊的含义，如下所示。

      * **UNSPECIFIED**
        父容器不对View有任何限制，要多大给多大，这种情况一般用于系统内部，表示一种测量的状态。
      * **EXACTLY**
        父容器已经检测出View所需要的精确大小，这个时候View的最终大小就是SpecSize所指定的值。它对应于LayoutParams中的match_parent和具体的数值这两种模式。
      * **AT_MOST**
        父容器指定了一个可用大小即SpecSize, View的大小不能大于这个值，具体是什么值要看不同View的具体实现。它对应于LayoutParams中的wrap_content。

    * MeasureSpec 和 LayoutParam

      对于顶级View（即DecorView）和普通View来说，MeasureSpec的转换过程略有不同。对于DecorView，其MeasureSpec由窗口的尺寸和其自身的LayoutParams来共同确定；对于普通View，其MeasureSpec由父容器的MeasureSpec和自身的LayoutParams来共同决定，MeasureSpec一旦确定后，onMeasure中就可以确定View的测量宽/高。

      

      ​                                                                             普通View的MeasureSpec的创建规则

      ![img](https://img.kancloud.cn/61/ed/61ed8d409cb1b09f5575c1f51ab7ae79_1351x364.png)

      

      前面已经提到，对于普通View，其MeasureSpec由父容器的MeasureSpec和自身的LayoutParams来共同决定，那么针对不同的父容器和View本身不同的LayoutParams, View就可以有多种MeasureSpec。这里简单说一下，当View采用固定宽/高的时候，不管父容器的MeasureSpec是什么，View的MeasureSpec都是精确模式并且其大小遵循Layoutparams中的大小。当View的宽/高是match_parent时，如果父容器的模式是精准模式，那么View也是精准模式并且其大小是父容器的剩余空间；如果父容器是最大模式，那么View也是最大模式并且其大小不会超过父容器的剩余空间。当View的宽/高是wrap_content时，不管父容器的模式是精准还是最大化，View的模式总是最大化并且大小不能超过父容器的剩余空间。可能读者会发现，在我们的分析中漏掉了UNSPECIFIED模式，那是因为这个模式主要用于系统内部多次Measure的情形，一般来说，我们不需要关注此模式。

      

  * **View的工作流程**

    View的工作流程主要是指measure、layout、draw这三大流程，即测量、布局和绘制，其中measure确定View的测量宽/高，layout确定View的最终宽/高和四个顶点的位置，而draw则将View绘制到屏幕上。

    1. **measure过程**

       **1.1 View的measure过程**

       View的measure过程由其measure方法来完成，measure方法是一个final类型的方法，这意味着子类不能重写此方法，在View的measure方法中会去调用View的onMeasure方法，因此只需要看onMeasure的实现即可，View的onMeasure方法如下所示。

       ```
           protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
               setMeasuredDimension(getDefaultSize(getSuggestedMinimumWidth(),
               widthMeasureSpec), getDefaultSize(getSuggestedMinimumHeight(),
               heightMeasureSpec));
           }
       ```

       上述代码很简洁，但是简洁并不代表简单，setMeasuredDimension方法会设置View宽/高的测量值，因此我们只需要看getDefaultSize这个方法即可：

       ```
           public static int getDefaultSize(int size, int measureSpec) {
               int result = size;
               int specMode = MeasureSpec.getMode(measureSpec);
               int specSize = MeasureSpec.getSize(measureSpec);
       
               switch (specMode) {
               case MeasureSpec.UNSPECIFIED:
                   result = size;
                   break;
               case MeasureSpec.AT_MOST:
               case MeasureSpec.EXACTLY:
                   result = specSize;
                   break;
               }
               return result;
           }
       ```

       可以看出，getDefaultSize这个方法的逻辑很简单，对于我们来说，我们只需要看AT_MOST和．EXACTLY这两种情况。简单地理解，其实getDefaultSize返回的大小就是measureSpec中的specSize，而这个specSize就是View测量后的大小，这里多次提到测量后的大小，是因为View最终的大小是在layout阶段确定的，所以这里必须要加以区分，但是几乎所有情况下View的测量大小和最终大小是相等的。
       至于UNSPECIFIED这种情况，一般用于系统内部的测量过程，在这种情况下，View的大小为getDefaultSize的第一个参数size，即宽/高分别为getSuggestedMinimumWidth和getSuggestedMinimumHeight这两个方法的返回值，看一下它们的源码：

       ```
           protected int getSuggestedMinimumWidth() {
               return (mBackground == null) ? mMinWidth : max(mMinWidth, mBackground.
               getMinimumWidth());
           }
       
           protected int getSuggestedMinimumHeight() {
               return (mBackground == null) ? mMinHeight : max(mMinHeight, mBackground.
               getMinimumHeight());
           }
       ```

       这里只分析getSuggestedMinimumWidth方法的实现，getSuggestedMinimumHeight和它的实现原理是一样的。从getSuggestedMinimumWidth的代码可以看出，如果View没有设置背景，那么View的宽度为mMinWidth，而mMinWidth对应于android:minWidth这个属性所指定的值，因此View的宽度即为android:minWidth属性所指定的值。这个属性如果不指定，那么mMinWidth则默认为0；如果View指定了背景，则View的宽度为max(mMinWidth, mBackground.getMinimumWidth())。mMinWidth的含义我们已经知道了，那么mBackground. getMinimumWidth()是什么呢？我们看一下Drawable的getMinimumWidth方法，如下所示。

       ```
           public int getMinimumWidth() {
               final int intrinsicWidth = getIntrinsicWidth();
               return intrinsicWidth > 0 ? intrinsicWidth : 0;
           }
       ```

       可以看出，getMinimumWidth返回的就是Drawable的原始宽度，前提是这个Drawable有原始宽度，否则就返回0。那么Drawable在什么情况下有原始宽度呢？这里先举个例子说明一下，ShapeDrawable无原始宽/高，而BitmapDrawable有原始宽/高（图片的尺寸），详细内容会在第6章进行介绍。
       **这里再总结一下getSuggestedMinimumWidth的逻辑**：如果View没有设置背景，那么返回android:minWidth这个属性所指定的值，这个值可以为0；如果View设置了背景，则返回android:minWidth和背景的最小宽度这两者中的最大值，getSuggestedMinimumWidth和getSuggestedMinimumHeight的返回值就是View在UNSPECIFIED情况下的测量宽/高。
       
       
       
       从getDefaultSize方法的实现来看，View的宽/高由specSize决定，所以我们可以得出如下结论：直接继承View的自定义控件需要重写onMeasure方法并设置wrap_content时的自身大小，否则在布局中使用wrap_content就相当于使用match_parent。为什么呢？这个原因需要结合上述代码和表4-1才能更好地理解。从上述代码中我们知道，如果View在布局中使用wrap_content，那么它的specMode是AT_MOST模式，在这种模式下，它的宽/高等于specSize；查表4-1可知，这种情况下View的specSize是parentSize，而parentSize是父容器中目前可以使用的大小，也就是父容器当前剩余的空间大小。很显然，View的宽/高就等于父容器当前剩余的空间大小，这种效果和在布局中使用match_parent完全一致。如何解决这个问题呢？也很简单，代码如下所示。
    
       ```
          protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
               super.onMeasure(widthMeasureSpec, heightMeasureSpec);
               int widthSpecMode = MeasureSpec.getMode(widthMeasureSpec);
               int widthSpecSize = MeasureSpec.getSize(widthMeasureSpec);
               int heightSpecMode = MeasureSpec.getMode(heightMeasureSpec);
               int heightSpecSize = MeasureSpec.getSize(heightMeasureSpec);
               if (widthSpecMode == MeasureSpec.AT_MOST && heightSpecMode ==
               MeasureSpec.AT_MOST) {
                   setMeasuredDimension(mWidth, mHeight);
               } else if (widthSpecMode == MeasureSpec.AT_MOST) {
                   setMeasuredDimension(mWidth, heightSpecSize);
               } else if (heightSpecMode == MeasureSpec.AT_MOST) {
                   setMeasuredDimension(widthSpecSize, mHeight);
               }
           }
       ```
    
     在上面的代码中，我们只需要给View指定一个默认的内部宽/高（mWidth和mHeight），并在wrap_content时设置此宽/高即可。对于非wrap_content情形，我们沿用系统的测量值即可，至于这个默认的内部宽/高的大小如何指定，这个没有固定的依据，根据需要灵活指定即可。如果查看TextView、ImageView等的源码就可以知道，针对wrap_content情形，它们的onMeasure方法均做了特殊处理，读者可以自行查看它们的源码。
    
     
    
     **1.2 ViewGroup的measure过程**
    
     对于ViewGroup来说，除了完成自己的measure过程以外，还会遍历去调用所有子元素的measure方法，各个子元素再递归去执行这个过程。和View不同的是，ViewGroup是一个抽象类，因此它没有重写View的onMeasure方法，但是它提供了一个叫measureChildren的方法，如下所示。
    
       ```
           protected void measureChildren(int widthMeasureSpec, int heightMeasureSpec) {
               final int size = mChildrenCount;
               final View[] children = mChildren;
                 for (int i = 0; i < size; ++i) {
                     final View child = children[i];
                     if ((child.mViewFlags & VISIBILITY_MASK) ! = GONE) {
                         measureChild(child, widthMeasureSpec, heightMeasureSpec);
                     }
                 }
             }
       ```
    
     从上述代码来看，ViewGroup在measure时，会对每一个子元素进行measure, measureChild这个方法的实现也很好理解，如下所示。
    
       ```
           protected void measureChild(View child, int parentWidthMeasureSpec,
                   int parentHeightMeasureSpec) {
               final LayoutParams lp = child.getLayoutParams();
       
               final int childWidthMeasureSpec = getChildMeasureSpec(parentWidth-
               MeasureSpec,
                       mPaddingLeft + mPaddingRight, lp.width);
               final int childHeightMeasureSpec = getChildMeasureSpec(parentHeight-
               MeasureSpec,
                       mPaddingTop + mPaddingBottom, lp.height);
       
               child.measure(childWidthMeasureSpec, childHeightMeasureSpec);
           }
       ```
    
       很显然，measureChild的思想就是取出子元素的LayoutParams，然后再通过getChildMeasureSpec来创建子元素的MeasureSpec，接着将MeasureSpec直接传递给View的measure方法来进行测量。getChildMeasureSpec的工作过程已经在上面进行了详细分析，通过表4-1可以更清楚地了解它的逻辑。
       我们知道，ViewGroup并没有定义其测量的具体过程，这是因为ViewGroup是一个抽象类，其测量过程的onMeasure方法需要各个子类去具体实现，比如LinearLayout、RelativeLayout等，为什么ViewGroup不像View一样对其onMeasure方法做统一的实现呢？那是因为不同的ViewGroup子类有不同的布局特性，这导致它们的测量细节各不相同，比如LinearLayout和RelativeLayout这两者的布局特性显然不同，因此ViewGroup无法做统一实现。下面就通过LinearLayout的onMeasure方法来分析ViewGroup的measure过程，其他Layout类型读者可以自行分析。
     首先来看LinearLayout的onMeasure方法，如下所示。
    
       ```
           protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
               if (mOrientation == VERTICAL) {
                   measureVertical(widthMeasureSpec, heightMeasureSpec);
               } else {
                   measureHorizontal(widthMeasureSpec, heightMeasureSpec);
               }
           }
       ```
    
     上述代码很简单，我们选择一个来看一下，比如选择查看竖直布局的LinearLayout的测量过程，即measureVertical方法，measureVertical的源码比较长，下面只描述其大概逻辑，首先看一段代码：
    
       ```
           // See how tall everyone is. Also remember max width.
           for (int i = 0; i < count; ++i) {
               final View child = getVirtualChildAt(i);
               ...
               // Determine how big this child would like to be. If this or
               // previous children have given a weight, then we allow it to
               // use all available space (and we will shrink things later
               // if needed).
               measureChildBeforeLayout(
                     child, i, widthMeasureSpec, 0, heightMeasureSpec,
                     totalWeight == 0 ? mTotalLength : 0);
       
               if (oldHeight ! = Integer.MIN_VALUE) {
                 lp.height = oldHeight;
               }
       
               final int childHeight = child.getMeasuredHeight();
               final int totalLength = mTotalLength;
               mTotalLength=Math.max(totalLength, totalLength+childHeight+lp.topMargin +
                     lp.bottomMargin + getNextLocationOffset(child));
           }
       ```
    
     从上面这段代码可以看出，系统会遍历子元素并对每个子元素执行measureChild-BeforeLayout方法，这个方法内部会调用子元素的measure方法，这样各个子元素就开始依次进入measure过程，并且系统会通过mTotalLength这个变量来存储LinearLayout在竖直方向的初步高度。每测量一个子元素，mTotalLength就会增加，增加的部分主要包括了子元素的高度以及子元素在竖直方向上的margin等。当子元素测量完毕后，LinearLayout会测量自己的大小，源码如下所示。
    
       ```
                 // Add in our padding
             mTotalLength += mPaddingTop + mPaddingBottom;
             int heightSize = mTotalLength;
             // Check against our minimum height
             heightSize = Math.max(heightSize, getSuggestedMinimumHeight());
             // Reconcile our calculated size with the heightMeasureSpec
            int heightSizeAndState=resolveSizeAndState(heightSize, heightMeasureSpec,
             0);
             heightSize = heightSizeAndState & MEASURED_SIZE_MASK;
             ...
             setMeasuredDimension(resolveSizeAndState(maxWidth, widthMeasureSpec,
             childState),
             heightSizeAndState);
       ```
    
     这里对上述代码进行说明，当子元素测量完毕后，LinearLayout会根据子元素的情况来测量自己的大小。针对竖直的LinearLayout而言，它在水平方向的测量过程遵循View的测量过程，在竖直方向的测量过程则和View有所不同。具体来说是指，如果它的布局中高度采用的是match_parent或者具体数值，那么它的测量过程和View一致，即高度为specSize；如果它的布局中高度采用的是wrap_content，那么它的高度是所有子元素所占用的高度总和，但是仍然不能超过它的父容器的剩余空间，当然它的最终高度还需要考虑其在竖直方向的padding，这个过程可以进一步参看如下源码：
    
       ```
           public static int resolveSizeAndState(int size, int measureSpec, int
           childMeasuredState) {
               int result = size;
               int specMode = MeasureSpec.getMode(measureSpec);
               int specSize =  MeasureSpec.getSize(measureSpec);
               switch (specMode) {
               case MeasureSpec.UNSPECIFIED:
                   result = size;
                   break;
               case MeasureSpec.AT_MOST:
                   if (specSize < size) {
                       result = specSize | MEASURED_STATE_TOO_SMALL;
                   } else {
                             result = size;
                         }
                         break;
                     case MeasureSpec.EXACTLY:
                         result = specSize;
                         break;
                     }
                     return result | (childMeasuredState&MEASURED_STATE_MASK);
                 }
       ```
    
     
    
     View的measure过程是三大流程中最复杂的一个，measure完成以后，通过getMeasured-Width/Height方法就可以正确地获取到View的测量宽/高。需要注意的是，在某些极端情况下，系统可能需要多次measure才能确定最终的测量宽/高，在这种情形下，在onMeasure方法中拿到的测量宽/高很可能是不准确的。一个比较好的习惯是在onLayout方法中去获取View的测量宽/高或者最终宽/高。
    
     
    
       上面已经对View的measure过程进行了详细的分析，现在考虑一种情况，比如我们想在Activity已启动的时候就做一件任务，但是这一件任务需要获取某个View的宽/高。读者可能会说，这很简单啊，在onCreate或者onResume里面去获取这个View的宽/高不就行了？读者可以自行试一下，实际上在onCreate、onStart、onResume中均无法正确得到某个View的宽/高信息，这是因为View的measure过程和Activity的生命周期方法不是同步执行的，因此无法保证Activity执行了onCreate、onStart、onResume时某个View已经测量完毕了，如果View还没有测量完毕，那么获得的宽/高就是0。
    
     
    
       有没有什么方法能解决这个问题呢？答案是有的，这里给出四种方法来解决这个问题：
       （1）Activity/View#onWindowFocusChanged。
       onWindowFocusChanged这个方法的含义是：View已经初始化完毕了，宽/高已经准备好了，这个时候去获取宽/高是没问题的。需要注意的是，onWindowFocusChanged会被调用多次，当Activity的窗口得到焦点和失去焦点时均会被调用一次。具体来说，当Activity继续执行和暂停执行时，onWindowFocusChanged均会被调用，如果频繁地进行onResume和onPause，那么onWindowFocusChanged也会被频繁地调用。典型代码如下：
    
       ```
           public void onWindowFocusChanged(boolean hasFocus) {
               super.onWindowFocusChanged(hasFocus);
               if (hasFocus) {
                 int width = view.getMeasuredWidth();
                       int height = view.getMeasuredHeight();
                   }
             }
       ```
    
       （2）view.post(runnable)。
       通过post可以将一个runnable投递到消息队列的尾部，然后等待Looper调用此runnable的时候，View也已经初始化好了。典型代码如下：
    
       ```
           protected void onStart() {
               super.onStart();
               view.post(new Runnable() {
       
                   @Override
                   public void run() {
                       int width = view.getMeasuredWidth();
                     int height = view.getMeasuredHeight();
                   }
               });
         }
       ```
    
       （3）ViewTreeObserver。
       使用ViewTreeObserver的众多回调可以完成这个功能，比如使用OnGlobalLayoutListener这个接口，当View树的状态发生改变或者View树内部的View的可见性发现改变时，onGlobalLayout方法将被回调，因此这是获取View的宽/高一个很好的时机。需要注意的是，伴随着View树的状态改变等，onGlobalLayout会被调用多次。典型代码如下：
    
       ```
           protected void onStart() {
               super.onStart();
       
               ViewTreeObserver observer = view.getViewTreeObserver();
               observer.addOnGlobalLayoutListener(new OnGlobalLayoutListener() {
       
                   @SuppressWarnings("deprecation")
                   @Override
                   public void onGlobalLayout() {
                             view.getViewTreeObserver().removeGlobalOnLayoutListener(this);
                             int width = view.getMeasuredWidth();
                           int height = view.getMeasuredHeight();
                         }
                     });
                 }
       ```
    
       （4）view.measure(int widthMeasureSpec, int heightMeasureSpec)。
     通过手动对View进行measure来得到View的宽/高。这种方法比较复杂，这里要分情况处理，根据View的LayoutParams来分：
       **match_parent**
       直接放弃，无法measure出具体的宽/高。原因很简单，根据View的measure过程，如表4-1所示，构造此种MeasureSpec需要知道parentSize，即父容器的剩余空间，而这个时候我们无法知道parentSize的大小，所以理论上不可能测量出View的大小。
       **具体的数值（dp/px）**
       比如宽/高都是100px，如下measure：
    
       ```java
           int widthMeasureSpec = MeasureSpec.makeMeasureSpec(100, MeasureSpec.
         EXACTLY);
           int heightMeasureSpec = MeasureSpec.makeMeasureSpec(100, MeasureSpec.
           EXACTLY);
             view.measure(widthMeasureSpec, heightMeasureSpec);
       ```
    
       **wrap_content**
       如下measure：
    
       ```java
           int widthMeasureSpec = MeasureSpec.makeMeasureSpec( (1 << 30) -1,
         MeasureSpec.AT_MOST);
           int heightMeasureSpec = MeasureSpec.makeMeasureSpec( (1 << 30) -1,
           MeasureSpec.AT_MOST);
               view.measure(widthMeasureSpec, heightMeasureSpec);
       ```
    
       注意到(1 << 30)-1，通过分析MeasureSpec的实现可以知道，View的尺寸使用30位二进制表示，也就是说最大是30个1（即2^30-1），也就是(1 << 30) -1，在最大化模式下，我们用View理论上能支持的最大值去构造MeasureSpec是合理的。
    
       关于View的measure，网络上有两个错误的用法。为什么说是错误的，首先其违背了系统的内部实现规范（因为无法通过错误的MeasureSpec去得出合法的SpecMode，从而导致measure过程出错），其次不能保证一定能measure出正确的结果。
    
       第一种错误用法：
    
     ```java
           int widthMeasureSpec = MeasureSpec.makeMeasureSpec(-1, MeasureSpec.
         UNSPECIFIED);
           int heightMeasureSpec = MeasureSpec.makeMeasureSpec(-1, MeasureSpec.
           UNSPECIFIED);
           view.measure(widthMeasureSpec, heightMeasureSpec);
     ```
    
     第二种错误用法：
    
     ```java
           view.measure(LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT)
     ```
    
       
    
    2. **layout过程**
    
       layout方法确定View本身的位置，而onLayout方法则会确定所有子元素的位置。
    
       ```java
           public void layout(int l, int t, int r, int b) {
               if ((mPrivateFlags3 & PFLAG3_MEASURE_NEEDED_BEFORE_LAYOUT) ! = 0) {
                   onMeasure(mOldWidthMeasureSpec, mOldHeightMeasureSpec);
                   mPrivateFlags3 &= ～PFLAG3_MEASURE_NEEDED_BEFORE_LAYOUT;
               }
       
               int oldL = mLeft;
               int oldT = mTop;
               int oldB = mBottom;
               int oldR = mRight;
       
               boolean changed = isLayoutModeOptical(mParent) ?
                       setOpticalFrame(l, t, r, b) : setFrame(l, t, r, b);
                 if (changed || (mPrivateFlags & PFLAG_LAYOUT_REQUIRED) == PFLAG_
                 LAYOUT_REQUIRED) {
                     onLayout(changed, l, t, r, b);
                     mPrivateFlags &= ～PFLAG_LAYOUT_REQUIRED;
       
                     ListenerInfo li = mListenerInfo;
                     if (li ! = null && li.mOnLayoutChangeListeners ! = null) {
                         ArrayList<OnLayoutChangeListener> listenersCopy =
                                 (ArrayList<OnLayoutChangeListener>)li.mOnLayout-
                                 ChangeListeners.clone();
                         int numListeners = listenersCopy.size();
                         for (int i = 0; i < numListeners; ++i) {
                             listenersCopy.get(i).onLayoutChange(this, l, t, r, b, oldL,
                             oldT, oldR, oldB);
                         }
                   }
                 }
        
                 mPrivateFlags &= ～PFLAG_FORCE_LAYOUT;
               mPrivateFlags3 |= PFLAG3_IS_LAID_OUT;
             }
       ```
    
       
    
       layout方法的大致流程如下：首先会通过setFrame方法来设定View的四个顶点的位置，即初始化mLeft、mRight、mTop和mBottom这四个值，View的四个顶点一旦确定，那么View在父容器中的位置也就确定了；接着会调用onLayout方法，这个方法的用途是父容器确定子元素的位置，和onMeasure方法类似，onLayout的具体实现同样和具体的布局有关，所以View和ViewGroup均没有真正实现onLayout方法。
    
       接下来，我们可以看一下LinearLayout的onLayout方法，如下所示。
    
    
    
           protected void onLayout(boolean changed, int l, int t, int r, int b) {
             if (mOrientation == VERTICAL) {
                   layoutVertical(l, t, r, b);
             } else {
                   layoutHorizontal(l, t, r, b);
               }
           }
       ```
    
       LinearLayout中onLayout的实现逻辑和onMeasure的实现逻辑类似，这里选择layoutVertical继续讲解，为了更好地理解其逻辑，这里只给出了主要的代码：
    
       ```java
           void layoutVertical(int left, int top, int right, int bottom) {
               ...
               final int count = getVirtualChildCount();
               for (int i = 0; i < count; i++) {
                   final View child = getVirtualChildAt(i);
                   if (child == null) {
                       childTop += measureNullChild(i);
                   } else if (child.getVisibility() ! = GONE) {
                       final int childWidth = child.getMeasuredWidth();
                       final int childHeight = child.getMeasuredHeight();
       
                       final LinearLayout.LayoutParams lp =
                               (LinearLayout.LayoutParams) child.getLayoutParams();
                       ...
                       if (hasDividerBeforeChildAt(i)) {
                           childTop += mDividerHeight;
                       }
       
                       childTop += lp.topMargin;
                       setChildFrame(child, childLeft, childTop + getLocationOffset
                       (child), childWidth, childHeight);
                     childTop += childHeight + lp.bottomMargin + getNextLocation-
                       Offset(child);
                     i += getChildrenSkipCount(child, i);
                   }
               }
           }
       ```
    
     这里分析一下layoutVertical的代码逻辑，可以看到，此方法会遍历所有子元素并调用setChildFrame方法来为子元素指定对应的位置，其中childTop会逐渐增大，这就意味着后面的子元素会被放置在靠下的位置，这刚好符合竖直方向的LinearLayout的特性。至于setChildFrame，它仅仅是调用子元素的layout方法而已，这样父元素在layout方法中完成自己的定位以后，就通过onLayout方法去调用子元素的layout方法，子元素又会通过自己的layout方法来确定自己的位置，这样一层一层地传递下去就完成了整个View树的layout过程。setChildFrame方法的实现如下所示。
    
       ```java
         private void setChildFrame(View child, int left, int top, int width, int
           height) {
                 child.layout(left, top, left + width, top + height);
             }
       ```
    
     我们注意到，setChildFrame中的width和height实际上就是子元素的测量宽/高，从下面的代码可以看出这一点：
    
       ```java
         final int childWidth = child.getMeasuredWidth();
           final int childHeight = child.getMeasuredHeight();
           setChildFrame(child, childLeft, childTop + getLocationOffset(child),
           childWidth, childHeight);
       ```
    
     而在layout方法中会通过setFrame去设置子元素的四个顶点的位置，在setFrame中有如下几句赋值语句，这样一来子元素的位置就确定了：
    
       ```java
         mLeft = left;
           mTop = top;
         mRight = right;
           mBottom = bottom;
       ```
    
       
    
       **View的getMeasuredWidth和getWidth这两个方法有什么区别**，至于getMeasuredHeight和getHeight的区别和前两者完全一样。为了回答这个问题，首先，我们看一下getwidth和getHeight这两个方法的具体实现：
    
       ```
           public final int getWidth() {
             return mRight - mLeft;
           }
     
           public final int getHeight() {
               return mBottom - mTop;
           }
       ```
    
     从getWidth和getHeight的源码再结合mLeft、mRight、mTop和mBottom这四个变量的赋值过程来看，getWidth方法的返回值刚好就是View的测量宽度，而getHeight方法的返回值也刚好就是View的测量高度。经过上述分析，现在我们可以回答这个问题了：在View的默认实现中，View的测量宽/高和最终宽/高是相等的，只不过测量宽/高形成于View的measure过程，而最终宽/高形成于View的layout过程，即两者的赋值时机不同，测量宽/高的赋值时机稍微早一些。因此，在日常开发中，我们可以认为View的测量宽/高就等于最终宽/高，但是的确存在某些特殊情况会导致两者不一致，下面举例说明。
    
     ```java
           public void layout(int l, int t, int r, int b) {
             super.layout(l, t, r + 100, b + 100);
           }
     ```
    
     上述代码会导致在任何情况下View的最终宽/高总是比测量宽/高大100px，虽然这样做会导致View显示不正常并且也没有实际意义，但是这证明了测量宽/高的确可以不等于最终宽/高。另外一种情况是在某些情况下，View需要多次measure才能确定自己的测量宽/高，在前几次的测量过程中，其得出的测量宽/高有可能和最终宽/高不一致，但最终来说，测量宽/高还是和最终宽/高相同。
    
     
    
  3. **draw过程**
    
     Draw过程就比较简单了，它的作用是将View绘制到屏幕上面。View的绘制过程遵循如下几步：
    
     （1）绘制背景background.draw(canvas)。
    
       （2）绘制自己（onDraw）。
    
       （3）绘制children（dispatchDraw）。
    
       （4）绘制装饰（onDrawScrollBars）。
    
       ```java
           public void draw(Canvas canvas) {
               final int privateFlags = mPrivateFlags;
               final boolean dirtyOpaque = (privateFlags & PFLAG_DIRTY_MASK) ==
               PFLAG_DIRTY_OPAQUE &&
                       (mAttachInfo == null || ! mAttachInfo.mIgnoreDirtyState);
               mPrivateFlags = (privateFlags & ～PFLAG_DIRTY_MASK) | PFLAG_DRAWN;
       
               /＊
               ＊ Draw traversal performs several drawing steps which must be executed
               ＊ in the appropriate order:
               ＊ in the appropriate order:
             ＊
             ＊       1. Draw the background
             ＊       2. If necessary, save the canvas' layers to prepare for fading
             ＊       3. Draw view's content
             ＊       4. Draw children
             ＊       5. If necessary, draw the fading edges and restore layers
             ＊       6. Draw decorations (scrollbars for instance)
             ＊/
       
            // Step 1, draw the background, if needed
            int saveCount;
       
            if (! dirtyOpaque) {
                 drawBackground(canvas);
            }
       
            // skip step 2 & 5 if possible (common case)
            final int viewFlags = mViewFlags;
            boolean horizontalEdges = (viewFlags & FADING_EDGE_HORIZONTAL) ! = 0;
            boolean verticalEdges = (viewFlags & FADING_EDGE_VERTICAL) ! = 0;
            if (! verticalEdges && ! horizontalEdges) {
                 // Step 3, draw the content
                 if (! dirtyOpaque) onDraw(canvas);
       
                 // Step 4, draw the children
                 dispatchDraw(canvas);
       
                 // Step 6, draw decorations (scrollbars)
                 onDrawScrollBars(canvas);
       
                 if (mOverlay ! = null && ! mOverlay.isEmpty()) {
                     mOverlay.getOverlayView().dispatchDraw(canvas);
                 }
     
                 // we're done...
                 return;
            }
            ...
         }
       ```
    
       

  * **requestLayout和invalidate区别**

    

* 参考资料
  
  * [Android自定义View教程目录](http://www.gcssloop.com/category/customview.html)
  * [《Android开发艺术探索》](http://static.kancloud.cn/alex_wsc/android_art/1828407)
  * [比较一下requestLayout和invalidate方法](https://juejin.cn/post/6904518722564653070#heading-0)
  * [invalidate、postInvalidate与requestLayout浅析](https://juejin.cn/post/6844903913196519431)



### Android 自定义View

* 知识点

  自定义View 需要具备的知识：View的绘制原理、View坐标体系、Input事件传递、自定义View 的分类、View的构造函数、自定义属性。前面三个知识点已经总结过了。

  * **自定义View的分类**

    1. 继承View重写onDraw方法

       这种方法主要用于实现一些不规则的效果，即这种效果不方便通过布局的组合方式来达到，往往需要静态或者动态地显示一些不规则的图形。很显然这需要通过绘制的方式来实现，即重写onDraw方法。采用这种方式需要自己支持wrap_content，并且padding也需要自己处理。

       

    2. 继承ViewGroup派生特殊的Layout

       这种方法主要用于实现自定义的布局，即除了LinearLayout、RelativeLayout、FrameLayout这几种系统的布局之外，我们重新定义一种新布局，当某种效果看起来很像几种View组合在一起的时候，可以采用这种方法来实现。采用这种方式稍微复杂一些，需要合适地处理ViewGroup的测量、布局这两个过程，并同时处理子元素的测量和布局过程。

       

    3. 继承特定的View（比如TextView）

       这种方法比较常见，一般是用于扩展某种已有的View的功能，比如TextView，这种方法比较容易实现。这种方法不需要自己支持wrap_content和padding等。

       

    4. 继承特定的ViewGroup（比如LinearLayout）

       这种方法也比较常见，当某种效果看起来很像几种View组合在一起的时候，可以采用这种方法来实现。采用这种方法不需要自己处理ViewGroup的测量和布局这两个过程。需要注意这种方法和方法2的区别，一般来说方法2能实现的效果方法4也都能实现，两者的主要差别在于方法2更接近View的底层。

  * **构造函数**

    无论是我们继承系统View还是直接继承View，都需要对构造函数进行重写，构造函数有多个，至少要重写其中一个才行。

    ```java
    public class TestView extends View {
        /**
         * 在java代码里new的时候会用到
         * @param context
         */
        public TestView(Context context) {
            super(context);
        }
    
        /**
         * 在xml布局文件中使用时自动调用
         * @param context
         */
        public TestView(Context context, @Nullable AttributeSet attrs) {
            super(context, attrs);
        }
    
        /**
         * 不会自动调用，如果有默认style时，在第二个构造函数中调用
         * @param context
         * @param attrs
         * @param defStyleAttr
         */
        public TestView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
            super(context, attrs, defStyleAttr);
        }
    
    
        /**
         * 只有在API版本>21时才会用到
         * 不会自动调用，如果有默认style时，在第二个构造函数中调用
         * @param context
         * @param attrs
         * @param defStyleAttr
         * @param defStyleRes
         */
        @RequiresApi(api = Build.VERSION_CODES.LOLLIPOP)
        public TestView(Context context, @Nullable AttributeSet attrs, int defStyleAttr, int defStyleRes) {
            super(context, attrs, defStyleAttr, defStyleRes);
        }
    }
    ```

    

  * **自定义属性**

    自定义属性分为以下几步：

    1. 自定义一个View
    2. 编写values/attrs.xml，在其中编写styleable和item等标签元素
    3. 在布局文件中View使用自定义的属性（注意namespace）
    4. 在View的构造方法中通过TypedArray获取

    

    自定义属性的声明文件

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <resources>
            <declare-styleable name="test">
                <attr name="text" format="string" />
                <attr name="testAttr" format="integer" />
            </declare-styleable>
        </resources>
    
    ```

    自定义View

    ```java
    public class MyTextView extends View {
        private static final String TAG = MyTextView.class.getSimpleName();
    
        //在View的构造方法中通过TypedArray获取
        public MyTextView(Context context, AttributeSet attrs) {
            super(context, attrs);
            TypedArray ta = context.obtainStyledAttributes(attrs, R.styleable.test);
            String text = ta.getString(R.styleable.test_testAttr);
            int textAttr = ta.getInteger(R.styleable.test_text, -1);
            Log.e(TAG, "text = " + text + " , textAttr = " + textAttr);
            ta.recycle();
        }
    }
    
    ```

    布局文件中使用

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        xmlns:app="http://schemas.android.com/apk/res/com.example.test"
        android:layout_width="match_parent"
        android:layout_height="match_parent" >
    
        <com.example.test.MyTextView
            android:layout_width="100dp"
            android:layout_height="200dp"
            app:testAttr="520"
            app:text="helloworld" />
    
    </RelativeLayout>
    
    ```

    

  * **自定义View须知**

    1. 让View支持wrap_content
       这是因为直接继承View或者ViewGroup的控件，如果不在onMeasure中对wrap_content做特殊处理，那么当外界在布局中使用wrap_content时就无法达到预期的效果，具体情形已经在4.3.1节中进行了详细的介绍，这里不再重复了。

       

    2. 如果有必要，让你的View支持padding
       这是因为直接继承View的控件，如果不在draw方法中处理padding，那么padding属性是无法起作用的。另外，直接继承自ViewGroup的控件需要在onMeasure和onLayout中考虑padding和子元素的margin对其造成的影响，不然将导致padding和子元素的margin失效。

       

    3. 尽量不要在View中使用HandIer，没必要
       这是因为View内部本身就提供了post系列的方法，完全可以替代Handler的作用，当然除非你很明确地要使用Handler来发送消息。

       

    4. View中如果有线程或者动画，需要及时停止，参考View#onDetachedFromWindow
       这一条也很好理解，如果有线程或者动画需要停止时，那么onDetachedFromWindow是一个很好的时机。当包含此View的Activity退出或者当前View被remove时，View的onDetachedFromWindow方法会被调用，和此方法对应的是onAttachedToWindow，当包含此View的Activity启动时，View的onAttachedToWindow方法会被调用。同时，当View变得不可见时我们也需要停止线程和动画，如果不及时处理这种问题，有可能会造成内存泄漏。

       

    5. View带有滑动嵌套情形时，需要处理好滑动冲突
       如果有滑动冲突的话，那么要合适地处理滑动冲突，否则将会严重影响View的效果，具体怎么解决滑动冲突请参看第3章。

    

* 参考资料
  * [《Android开发艺术探索》](http://static.kancloud.cn/alex_wsc/android_art/1828407)
  * [Android自定义View全解](https://juejin.cn/post/6844903607855218702#heading-17)



### 屏幕适配

* 知识点
  * 将 dp 单位转换为像素单位
  
    `px = dp * (dpi / 160)` 
  
    `DisplayMetrics.density` 字段根据当前像素密度指定将 `dp` 单位转换为像素时所必须使用的缩放系数。在中密度屏幕上，`DisplayMetrics.density` 等于 1.0；在高密度屏幕上，它等于 1.5；在超高密度屏幕上，等于 2.0；在低密度屏幕上，等于 0.75。此数字是一个系数，用其乘以 `dp` 单位，即可得出当前屏幕的实际像素数。
  
  * 使用预缩放的配置值
  
    您可以使用 `ViewConfiguration` 类来获取 Android 系统常用的距离、速度和时间。例如，可通过 `getScaledTouchSlop()` 来获取框架用作滚动阈值的距离（以像素为单位）：
  
    ```java
        private val GESTURE_THRESHOLD_DP = ViewConfiguration.get(myContext).scaledTouchSlop
    ```
  
    `ViewConfiguration` 中以 `getScaled` 前缀开头的方法确定会返回不管当前屏幕密度为何都会正常显示的像素值。
    
  * 建议在xdhpi中作图
* 参考资料
  * [Android机型适配终极篇](https://zhuanlan.zhihu.com/p/92083368) 
  * [支持不同的像素密度 - Android官网](https://developer.android.google.cn/training/multiscreen/screendensities?hl=zh-cn)



## 4. 动画

* 知识点
  * **动画类型（PropertyAnimation、Layout、Drawable、Fragment、Activity（overridePendingTransition、Transitions）**

  * **View动画**

    * 帧动画
    * 补间动画

  * **Layout动画**

    * 使用[android:layoutAnimation](http://developer.android.com/reference/android/view/ViewGroup.html#attr_android:layoutAnimation) 属性

      ```xml
      <LinearLayout 
          ...
          android:layoutAnimation="@anim/layout_bottom_to_top_slide" />
      ```

  * **Activity动画**

    * 使用 [overridePendingTransition](http://developer.android.com/reference/android/app/Activity.html#overridePendingTransition(int, int)) 

      ```java
      Intent i = new Intent(MainActivity.this, SecondActivity.class);
      startActivity(i);
      overridePendingTransition(R.anim.right_in, R.anim.left_out);
      ```

    * 使用 Transtion动画（共享元素）

      

  * **属性动画**

    * 可以做动画的属性有

      | Property                                                     | Description          |
      | :----------------------------------------------------------- | :------------------- |
  | `alpha`                                                      | Fade in or out       |
      | `rotation`, `rotationX`, `rotationY`                         | Spin or flip         |
  | `scaleX`, `scaleY`                                           | Grow or shrink       |
      | `x`, `y`, `z`                                                | Position             |
  | `translationX`, `translationY`, **`translationZ` (API 21+)** | Offset from Position |
  
* ValueAnimator 和 ObjectAnimator 
  
  * ValueAnimator可以通过valueAnim.addUpdateListener来自定义动画
    
        ```java
        // Construct the value animator and define the range
        ValueAnimator valueAnim = ValueAnimator.ofFloat(0, 1);
        // Animate over the course of 700 milliseconds
        valueAnim.setDuration(700);
        // Choose an interpolator
        valueAnim.setInterpolator(new DecelerateInterpolator());
        // Define how to update the view at each "step" of the animation
        valueAnim.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
          @Override
            public void onAnimationUpdate(ValueAnimator animation) {
              float animatedValue = (float) animation.getAnimatedValue();
              // Use the animated value to affect the view
          }
        });
    // Start the animation if not already running
        if (!valueAnim.isStarted()) {
          valueAnim.start();
        }
        ```
    
    ​    
    
    * 动画监听
    
      * AnimatorListenerAdapter 和 Animator.AnimatorListener
      * AnimatorUpdateListener，它会监听整个动画过程，动画是由许多帧组成的，每播放一帧，onAnimationUpdate就会被调用一次。

    * 插值器与估值器		

      * 插值器（Interpolator）

        1. 作用：设置属性值从初始值过渡到结束值 的变化规律，如匀速、加速 & 减速等等，即确定了 动画效果变化的模式，如匀速变化、加速变化等等。

        2. 使用：
    
           ```xml
         <?xml version="1.0" encoding="utf-8"?>
           <!--通过资源ID设置插值器-->
           <scale xmlns:android="http://schemas.android.com/apk/res/android"
               android:interpolator="@android:anim/overshoot_interpolator"
               android:duration="3000"
               android:startOffset="1000"
           
               android:fromXScale="0.0"
               android:toXScale="2"
               android:fromYScale="0.0"
               android:toYScale="2"
               android:pivotX="50%"
               android:pivotY="50%">
           </scale>
           ```
    
           ```java
           Animation scaleAnimation = new ScaleAnimation(0, 2, 0, 2, Animation.RELATIVE_TO_SELF, 0.5f, Animation.RELATIVE_TO_SELF, 0.5f);
           scaleAnimation.setDuration(3000);
           //scaleAnimation.setInterpolator(MainActivity.this,android.R.anim.overshoot_interpolator); 通过设置资源ID方式实现，等同于下面创建对象方式
           //创建对应的插值器类对象
           Interpolator overShootInterpolator = new OvershootInterpolator();
           //给动画设置插值器
           scaleAnimation.setInterpolator(overShootInterpolator);
           //播放动画
           button.startAnimation(scaleAnimation);
           ```
    
           
    
        3. 自定义插值器
    
       **根据动画的进度（0%-100%）计算出当前属性值改变的百分比**。自定义插值器需要实现 Interpolator / TimeInterpolator接口 & 复写getInterpolation()方法。
    
       > 补间动画实现Interpolator接口；属性动画一般实现TimeInterpolator接口
           >
       > TimeInterpolator接口是属性动画中新增的，用于兼容Interpolator接口，这使得所有过去的Interpolator实现类也可以直接在属性动画使用。
    
       ```java
           // 匀速差值器：LinearInterpolator
           @HasNativeInterpolator
               public class LinearInterpolator extends BaseInterpolator implements NativeInterpolatorFactory {
                   // 仅贴出关键代码
                   ...
                   public float getInterpolation(float input) {
                   return input;
                       /*没有对input值进行任何逻辑处理，直接返回
                    即input值 = fraction值
                        因为input值是匀速增加的，因此fraction值也是匀速增加的，
                    所以动画的运动情况也是匀速的，所以是匀速插值器。*/
                   }
               }
           
               // 先加速再减速 差值器：AccelerateDecelerateInterpolator
               @HasNativeInterpolator
               public class AccelerateDecelerateInterpolator implements Interpolator, NativeInterpolatorFactory {
                   // 仅贴出关键代码
                   ...
                   public float getInterpolation(float input) {
                       return (float) (Math.cos((input + 1) * Math.PI) / 2.0f) + 0.5f;
                       /*input的运算逻辑如下：
                        使用了余弦函数，因input的取值范围是0到1，那么cos函数中的取值范围就是π到2π。
                        而cos(π)的结果是-1，cos(2π)的结果是1
                        所以该值除以2加上0.5后，getInterpolation()方法最终返回的结果值还是在0到1之间。
                        只不过经过了余弦运算之后，最终的结果不再是匀速增加的了，而是经历了一个先加速后减速的过程
                        所以最终，fraction值 = 运算后的值 = 先加速后减速，所以该差值器是先加速再减速的。*/
                   }
             }
       ```
    
      ​     
    
      * 估值器（TypeEvaluator）
    
        1. 作用：设置属性值从初始值过渡到结束值的变化具体数值。**根据当前属性改变的百分比来计算改变后的属性值。**
    
        2. 使用
    
           ```java
           // 在第4个参数中传入对应估值器类的对象
           ObjectAnimator anim = ObjectAnimator.ofObject(button, "height", new Evaluator()，1，3);
           ```
    
           
    
        3. 自定义估值器
    
           ```java
           // FloatEvaluator实现了TypeEvaluator接口
           public class FloatEvaluator implements TypeEvaluator {  
               // 重写evaluate()
               // 参数说明
               // fraction：表示动画完成度（根据它来计算当前动画的值）
               // startValue、endValue：动画的初始值和结束值
               public Object evaluate(float fraction, Object startValue, Object endValue) {  
                   float startFloat = ((Number) startValue).floatValue();  
                   return startFloat + fraction * (((Number) endValue).floatValue() - startFloat);  
                   // 初始值过渡到结束值的算法是：
                   // 1. 用结束值减去初始值，算出它们之间的差值
                   // 2. 用上述差值乘以fraction系数
                   // 3. 再加上初始值，就得到当前动画的值
               }  
           }
           ```
  
* 参考资料
  * [Animations -  CodePath - 使用篇](https://guides.codepath.com/android/Animations)
  * [Android动画中篇（插值器、估值器）](https://www.jianshu.com/p/676bb3b4d71d)
  * [Android动画FillEnabled、FillBefore、FillAfter理解](https://blog.csdn.net/wiker_yong/article/details/40424667)
  * [Getting Started with Activity & Fragment Transitions (part 1)](https://www.androiddesignpatterns.com/2014/12/activity-fragment-transitions-in-android-lollipop-part1.html)
  * 《Android开发艺术探索》



## 5. APP 后台任务

* 知识点
  * 决策树
    	![此决策树可帮助您确定哪个类别最适合您的后台任务](https://developer.android.google.cn/images/guide/background/task-category-tree.png)
    
    概括一下：**及时任务** 使用 Kotlin 协程、线程池、RxJava等，特殊情况使用 前台服务；**延期任务**使用 WorkManager、JobSchedule；**精确任务** 使用 AlarmManager。
    
    
    
  * WorkManager

    WorkManager 是一个 API，可供您轻松调度那些即使在退出应用或重启设备后仍应运行的可靠异步任务。在后台，WorkManager 根据以下条件使用底层作业来调度服务：

    <img src="https://developer.android.google.cn/images/topic/libraries/architecture/workmanager/overview-criteria.png" alt="如果设备在 API 级别 23 或更高级别上运行，系统会使用 JobScheduler。在 API 级别 14-22 上，系统会使用 GcmNetworkManager（如果可用），否则会使用自定义 AlarmManager 和 BroadcastReciever 实现作为备用。" style="zoom: 50%;" />

    <img src="https://upload-images.jianshu.io/upload_images/8398510-911fe131c5a04cef?imageMogr2/auto-orient/strip|imageView2/2/w/1000/format/webp" alt="WorkManager架构图" style="zoom: 67%;" />

    ① 给WorkManager发送工作请求WorkRequest，WorkRequest中包装了Worker。

    ② WorkManager将该请求的相关参数放入WorkManager的数据库中。

    ③ WorkManager根据设备版本、是否是前台任务等情况将请求操作传递给JobScheduler或者AlarmManager等部件。

    ④ 检查Worker是否满足约束条件，当满足约束条件时调用执行Worker。

    

  * **JobScheduer、 JobService、JobInfo**

    1. 创建Job Service

    ```java
    public class MyJobService extends JobService {
    private static final String LOG_TAG ="ms" ;
    @Override
    public boolean onStartJob(JobParameters params) {
        if (isNetWorkConnected()){
        //在这里执行下载任务
            new SimpleDownloadTask().execute(params);
            return true;
        }
        return false;
    }
    @Override
    public boolean onStopJob(JobParameters params) {
        return false;
    }
    ```

    

    2. JobScheduler的schedule过程：

    ```java
     JobScheduler scheduler = (JobScheduler) getSystemService(Context.JOB_SCHEDULER_SERVICE);  
     ComponentName jobService = new ComponentName(this, MyJobService.class);
    
     JobInfo jobInfo = new JobInfo.Builder(123, jobService) //任务Id等于123
             .setMinimumLatency(5000)// 任务最少延迟时间 
             .setOverrideDeadline(60000)// 任务deadline，当到期没达到指定条件也会开始执行 
             .setRequiredNetworkType(JobInfo.NETWORK_TYPE_UNMETERED)// 网络条件，默认值NETWORK_TYPE_NONE
             .setRequiresCharging(true)// 是否充电 
             .setRequiresDeviceIdle(false)// 设备是否空闲
             .setPersisted(true) //设备重启后是否继续执行
             .setBackoffCriteria(3000，JobInfo.BACKOFF_POLICY_LINEAR) //设置退避/重试策略
             .build();  
     scheduler.schedule(jobInfo);
    ```

    3. JobScheduler的cancel过程：

    ```java
     scheduler.cancel(123); //取消jobId=123的任务
     scheduler.cancelAll(); //取消当前uid下的所有任务
    ```

    

  

* 参考资料
  
  * [官网文档 - 后台任务](https://developer.android.com/guide/background)
  * [Android架构组件JetPack之WorkManager完全解析（五）](https://juejin.cn/post/6844904001574535176)
  * [Android WorkManager](https://www.jianshu.com/p/444eb98724e8)
  * [Notifications (Persistent Notices on the Dashboard) -  CodePath - 使用篇](https://github.com/codepath/android_guides/wiki/Notifications)
  * [Notification 使用](http://blog.csdn.net/siobhan/article/details/50856433)



## 6. APP 存储&持久化

### 应用数据和文件

* 知识点

  * 存储位置的类别

    - **应用专属存储空间**：存储仅供应用使用的文件，可以存储到内部存储卷中的专属目录或外部存储空间中的其他专属目录。使用内部存储空间中的目录保存其他应用不应访问的敏感信息。
    - **共享存储**：存储您的应用打算与其他应用共享的文件，包括媒体、文档和其他文件。
    - **偏好设置**：以键值对形式存储私有原始数据。
    - **数据库**：使用 Room 持久性库将结构化数据存储在专用数据库中。

    |                                                              | 内容类型                                 | 访问方法                                                     | 所需权限                                                     | 其他应用是否可以访问？                                       | 卸载应用时是否移除文件？ |
    | :----------------------------------------------------------- | :--------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | ------------------------ |
    | [应用专属文件](https://developer.android.google.cn/training/data-storage/app-specific) | 仅供您的应用使用的文件                   | 从内部存储空间访问，可以使用 `getFilesDir()` 或 `getCacheDir()` 方法  从外部存储空间访问，可以使用 `getExternalFilesDir()` 或 `getExternalCacheDir()` 方法 | 从内部存储空间访问不需要任何权限  如果应用在搭载 Android 4.4（API 级别 19）或更高版本的设备上运行，从外部存储空间访问不需要任何权限 | 如果文件存储在内部存储空间中的目录内，则不能访问  如果文件存储在外部存储空间中的目录内，则可以访问 | 是                       |
    | [媒体](https://developer.android.google.cn/training/data-storage/shared/media) | 可共享的媒体文件（图片、音频文件、视频） | `MediaStore` API                                             | 在 Android 10（API 级别 29）或更高版本中，访问其他应用的文件需要 `READ_EXTERNAL_STORAGE` 或 `WRITE_EXTERNAL_STORAGE` 权限  在 Android 9（API 级别 28）或更低版本中，访问**所有**文件均需要相关权限 | 是，但其他应用需要 `READ_EXTERNAL_STORAGE` 权限              | 否                       |
    | [文档和其他文件](https://developer.android.google.cn/training/data-storage/shared/documents-files) | 其他类型的可共享内容，包括已下载的文件   | 存储访问框架                                                 | 无                                                           | 是，可以通过系统文件选择器访问                               | 否                       |
    | [应用偏好设置](https://developer.android.google.cn/training/data-storage/shared-preferences) | 键值对                                   | [Jetpack Preferences](https://developer.android.google.cn/guide/topics/ui/settings/use-saved-values) 库 | 无                                                           | 否                                                           | 是                       |
    | 数据库                                                       | 结构化数据                               | [Room](https://developer.android.google.cn/training/data-storage/room) 持久性库 | 无                                                           | 否                                                           | 是                       |

  

  * 对外部存储空间的访问和所需权限
    * Android 为对外部存储空间的读写访问定义了以下权限：[`READ_EXTERNAL_STORAGE`](https://developer.android.google.cn/reference/android/Manifest.permission#READ_EXTERNAL_STORAGE) 和 [`WRITE_EXTERNAL_STORAGE`](https://developer.android.google.cn/reference/android/Manifest.permission#WRITE_EXTERNAL_STORAGE)。
  * 对文件操作的最佳做法
    * 请勿反复打开和关闭文件
    * 共享单个文件（如果您需要与其他应用共享特定文件，请使用 FileProvider API 。 如果您需要向其他应用提供数据，可以使用内容提供器。）

  

  

* 参考资料
  
  * [官网文档 - 应用数据存储](https://developer.android.google.cn/training/data-storage) 
  * [Android总结 - 保存数据](https://blog.csdn.net/siobhan/article/details/51145498)



### 应用专属空间

* 内部空间
* 外部空间（要先查看可用空间、选择物理存储位置）
* 查询可用空间
  *  getAllocatableBytes() 



### 共享的存储空间

- **媒体内容**：系统提供标准的公共目录来存储这些类型的文件，这样用户就可以将所有照片保存在一个公共位置，将所有音乐和音频文件保存在另一个公共位置，依此类推。您的应用可以使用此平台的 [`MediaStore`](https://developer.android.google.cn/reference/android/provider/MediaStore) API 访问此内容。

- **文档和其他文件**：系统有一个特殊目录，用于包含其他文件类型，例如 PDF 文档和采用 EPUB 格式的图书。您的应用可以使用此平台的存储访问框架访问这些文件。

  > DocumentsUI



### Preferences

* 创建新的共享偏好设置文件或访问已有共享偏好设置文件

  * `getSharedPreferences()` - 如果您需要多个由名称（使用第一个参数指定）标识的共享偏好设置文件，则使用此方法。您可以从您的应用中的任何 `Context` 调用此方法。
  * `getPreferences()` - 如果您只需使用 Activity 的一个共享首选项，请从 `Activity` 中使用此方法。由于这会检索属于该 Activity 的默认共享偏好设置文件，因此您无需提供名称。

  > 如果您使用 `SharedPreferences` API 保存**应用设置**，则应改用 `getDefaultSharedPreferences()` 获取整个应用的默认共享偏好设置文件。

* 写入

  *  `SharedPreferences` 调用 `edit()` 以创建一个 `SharedPreferences.Editor`。
  * `apply()` 会立即更改内存中的 `SharedPreferences` 对象，但会将更新**异步**写入磁盘。 `commit()` 将数据**同步**写入磁盘。



### Provider

* 知识点
  * 设置权限
  * 创建ContentProvider
    * 设计数据存储
    * 设计内容URI
    * 实现ContentProvider类
    * 实现内容提供程序 MIME 类型
    * 实现内容提供程序权限
  * 读取或操作CP数据
  * 批处理
    * bulkInsert
    * ContentProviderOperation 和 ContentResolver.applyBatch()
  * 创建和使用FileProvider
  * 自定义文档提供程序  DocumentsProvider
  
* 参考资料
  * [ContentProvider基础 - 官方](https://developer.android.google.cn/guide/topics/providers/content-provider-basics)
  * [ContentProvider 的批处理操作](https://blog.csdn.net/siobhan/article/details/54983233)
  * [FileProvider的创建](https://developer.android.google.cn/reference/androidx/core/content/FileProvider?hl=en#ProviderDefinition)



### SQLite

* 知识点

  * SQLiteOpenHelper （创建、CRUD）
  * 升级、降级（逐版本升级）
  * ORMs(数据库框架)
  
* 参考资料

  * [Local Databases with SQLiteOpenHelper - CodePath - 使用篇](https://guides.codepath.com/android/Local-Databases-with-SQLiteOpenHelper)

  * [绝对值得一看的Android数据库升级攻略](https://blog.csdn.net/s003603u/article/details/53942411)

    

### 访问所有文件

* 知识点

  * 请求权限

    * An app can request All files access from the user by doing the following:

      1. Declare the [`MANAGE_EXTERNAL_STORAGE`](https://developer.android.google.cn/reference/android/Manifest.permission#MANAGE_EXTERNAL_STORAGE) permission in the manifest.
      2. Use the [`ACTION_MANAGE_ALL_FILES_ACCESS_PERMISSION`](https://developer.android.google.cn/reference/android/provider/Settings#ACTION_MANAGE_ALL_FILES_ACCESS_PERMISSION) intent action to direct users to a system settings page where they can enable the following option for your app: **Allow access to manage all files**.

      To determine whether your app has been granted the `MANAGE_EXTERNAL_STORAGE` permission, call [`Environment.isExternalStorageManager()`](https://developer.android.google.cn/reference/android/os/Environment#isExternalStorageManager()).

* 参考资料

  * [管理存储设备上的所有文件](https://developer.android.google.cn/training/data-storage/manage-all-files)



### DataStore

Jetpack DataStore 是一种数据存储解决方案，允许您使用[协议缓冲区](https://developers.google.com/protocol-buffers)存储键值对或类型化对象。DataStore 使用 Kotlin 协程和 Flow 以异步、一致的事务方式存储数据。

DataStore 提供两种不同的实现：Preferences DataStore 和 Proto DataStore。

- **Preferences DataStore** 使用键存储和访问数据。此实现不需要预定义的架构，也不确保类型安全。
- **Proto DataStore** 将数据作为自定义数据类型的实例进行存储。此实现要求您使用[协议缓冲区](https://developers.google.com/protocol-buffers)来定义架构，但可以确保类型安全。



### MMKV

MMKV——基于 mmap 的高性能通用 key-value 组件。



## 7. APP 网络操作

### 网络基础

* 知识点

  * http & https的区别 

    1、https协议需要到ca申请证书，一般免费证书较少，因而需要一定费用。
    2、http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议。
    3、http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
    4、http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。

    

* 参考资料

  * [HTTPS 理论详解与实践](https://segmentfault.com/a/1190000004985253)
  * [详解Https是如何确保安全的？](http://www.codeceo.com/article/https-make-safe.html)
  * [请简述 Http 与 Https 的区别？](https://github.com/Moosphan/Android-Daily-Interview/issues/71)



### Android原生网络API

* 知识点

  * URL、HttpURLConnection、getInputStream、getOutputStream

  ```java
  // 1. Declare a URL Connection
  URL url = new URL("http://www.google.com");
  HttpURLConnection conn = (HttpURLConnection) url.openConnection();
  // 2. Open InputStream to connection
  conn.connect();
  InputStream in = conn.getInputStream();
  // 3. Download and decode the string response using builder
  StringBuilder stringBuilder = new StringBuilder();
  BufferedReader reader = new BufferedReader(new InputStreamReader(in));
  String line;
  while ((line = reader.readLine()) != null) {
      stringBuilder.append(line);
  }
  
  ```

  

* 参考资料
  
  * [ Sending and Managing Network Requests (API Calls, Image Downloading) - CodePath - 使用篇](https://github.com/codepath/android_guides/wiki/Sending-and-Managing-Network-Requests)



### 文件下载

* 知识点

  * DownloadManager ，官方提供的下载工具，可以下载文件、监听进度和状态、状态栏显示等。

    * [DownloadManager.Query](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.android.google.cn%2Freference%2Fandroid%2Fapp%2FDownloadManager.Query)     主要用于查询下载的信息

    * [DownloadManager.Request](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.android.google.cn%2Freference%2Fandroid%2Fapp%2FDownloadManager.Request)  主要用于发起一个下载请求（其中可以添加下载的配置，例如Header等信息）

      

  * 多线程断点下载

    * Http 怎么支持断点续传的？

      主要是通过头部的两个参数：Range 和 Content Range 来实现的。客户端发请求时对应的是 Range ，服务器端响应时对应的是 Content-Range。

    * 使用断点续传和不使用断点续传的响应内容区别

      不使用断点续传：` HTTP/1.1 200 Ok `

      使用断点续传：`HTTP/1.1 206 Partial Content`

    * 检查服务器是否支持断点续传

      使用 curl 进行检测，可以看出以下的几个关键信息

      > HTTP/1.1 206 Partial Content
      >
      > Content-Range: bytes 10-222/7877
      >
      > Etag: "1ec5-502264e2ae4c0"
      >
      > Last-Modified: Wed, 03 Sep 2014 10:00:27 GMT

    * 处理请求资源发生改变的问题

    * 断点续传思路

      1. 断点 ==> 当前线程已经下载完成的数据长度。每当线程停止时就把已下载的数据长度写入记录文件，当重新下载时，从记录文件读取已经下载了的长度。而这个长度就是所需要的断点。
      2. 续传 ==> 向服务器请求上次线程停止位置之后的数据。可以通过设置网络请求参数，请求服务器从指定的位置开始读取数据。
      3. 多线程断点续传便是在单线程的断点续传上延伸的，而多线程断点续传是把整个文件分割成几个部分，每个部分由一条线程执行下载，而每一条下载线程都要实现断点续传功能。

      ![img](https://upload-images.jianshu.io/upload_images/1824042-843517c30becdcd6?imageMogr2/auto-orient/strip|imageView2/2/w/266/format/webp)

      

      

* 参考资料
  
  * [多线程断点下载](http://godcoder.me/tags/多线程下载/)
  * [Android Okhttp 断点续传面试解析](https://juejin.cn/post/6844903854115389447)
  * [Android多线程断点续传下载](https://www.jianshu.com/p/2b82db0a5181)
  * [接锅太急？DownloadManager助你一臂之力](https://juejin.cn/post/6844903768987795470)



### Socket

* 参考资料
  * [Sending and Receiving Data with Sockets - CodePath - 使用篇](http://guides.codepath.com/android/Sending-and-Receiving-Data-with-Sockets)



### WebSocket

* 知识点
  * WebSocket基于TCP并复用HTTP的握手通道， 是一个全双工的长连接应用层协议，可以通过它实现服务端到客户端主动的推送通信。
  * OkHttp 中使用 WebSocket 的关键在于 newWebSocket() 方法以及 WebSocketListener 这个抽象类，最终连接建立完毕后，可以通过 WebSocket 对象向对端发送消息；
  * WebSocket 鉴权，可以利用握手阶段的 HTTP 请求中，添加 Header 或者 URL 参数来实现；
  * WebSocket 的保活和心跳，需要定时发送 PING 帧，发送的时间间隔，在 OkHttp 中可以通过 pingInterval() 方法设置；
  * WebSocket可以用于实现即时通讯功能。
* 参考资料
  * [聊聊OkHttp实现WebSocket细节，包括鉴权和长连接保活及其原理！](https://juejin.cn/post/6844904100224581646)
  * [WebSocket：5分钟从入门到精通](https://juejin.cn/post/6844903544978407431#heading-0)



### 网络框架

* [Retrofit](#Retrofit)
* [okhttp](#okhttp)



## 8. APP架构

### 综合知识

* 参考资料
  * [彻底理解Android架构](https://zhuanlan.zhihu.com/p/23772285)
  * [官网 Android架构组件](https://developer.android.com/topic/libraries/architecture)



### Android 官方架构组件



* 常见的架构原则
  * 分离关注点
  * 通过模型（数据）驱动界面

![](https://developer.android.google.cn/topic/libraries/architecture/images/final-architecture.png?hl=zh-cn)



#### Lifecycle

* **生命周期**

  [`Lifecycle`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle) 是一个类，用于存储有关组件（如 Activity 或 Fragment）的生命周期状态的信息，并允许其他对象观察此状态。

  [`Lifecycle`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle) 使用两种主要枚举跟踪其关联组件的生命周期状态：

  * 事件
  * 状态

  ![](https://developer.android.google.cn/images/topic/libraries/architecture/lifecycle-states.svg?dcb_=0.07736335048381582)



* **LifecycleOwner、  Lifecycle、LifecycleObserver ** 
  
  * LifecycleOwner 表示具备 Lifecycle 属性，一个接口类。如果您有一个自定义类并希望使其成为 [`LifecycleOwner`](https://developer.android.google.cn/reference/androidx/lifecycle/LifecycleOwner)，您可以使用 [LifecycleRegistry](https://developer.android.google.cn/reference/androidx/lifecycle/LifecycleRegistry) 类，但需要将事件转发到该类
  * Lifecycle 定义一个类具有Android生命周期
  * LifecycleObserver 生命周期变化的回调接口
  
* **处理 ON_STOP 事件**

* **实现原理**

  SupportActivity 在 onCreate 方法里注入了 ReportFragment ，通过 Fragment 的机制来实现生命周期的监听。通过ReportFragmengt生命周期中调用 dispatchXXX 和 dispatch状态。



#### LiveData & ViewModel

[`LiveData`](https://developer.android.google.cn/reference/androidx/lifecycle/LiveData?hl=zh-cn) 是一种可观察的数据存储器类，具有生命周期感知能力。[`ViewModel`](https://developer.android.google.cn/reference/androidx/lifecycle/ViewModel?hl=zh-cn) 类旨在以注重生命周期的方式存储和管理界面相关的数据。[`ViewModel`](https://developer.android.google.cn/reference/androidx/lifecycle/ViewModel?hl=zh-cn) 类让数据可在发生屏幕旋转等配置更改后继续留存。

**ViewModel的实现原理**

1. 当Activity由于配置信息发生改变而重建的时候，会保存一个`Activity.NonConfigurationInstances`对象。

2. AppCompatActivity的间接父类ComponentActivity在此过程中保存了一个`ComponentActivity.NonConfigurationInstances`到`Activity.NonConfigurationInstances`中。`ComponentActivity.NonConfigurationInstances`中保存了AppCompatActivity当前的`ViewModelStore`对象。ViewModelStore对象中存储着当前AppCompatActivity的所有ViewModel对象。

3. 在新创建的AppCompatActivity的attach方法中恢复了`ViewModelStore`对象。该方法在onCreate方法之前被调用。

4. 在新创建的AppCompatActivity的onCreate方法中可以获取到恢复了的`ViewModelStore`对象。从而可以获取对应的ViewModel对象。



#### Room



#### 参考

* [LiveData 概览 - Android官网](https://developer.android.google.cn/topic/libraries/architecture/livedata#java)
* [Android Jetpack - 刘望舒](http://liuwangshu.cn/tags/Android-Jetpack/)
* [学习Android Jetpack? 实战和教程这里全都有！](https://juejin.cn/post/6844903889574051848)
* [【AAC 系列二】深入理解架构组件的基石：Lifecycle](https://juejin.cn/post/6844903842589442062)
* [“终于懂了“系列：Jetpack AAC完整解析（一）Lifecycle 完全掌握！](https://juejin.cn/post/6893870636733890574)
* [ViewModel原理分析](https://www.jianshu.com/p/e5c363255617)



### MVVM

* 知识点

  **View: **对应于Activity和XML，负责View的绘制以及与用户交互。 **Model: **实体模型。 **ViewModel: **负责完成View与Model间的交互，负责业务逻辑。

  

* 参考资料
  
  *  [如何构建Android MVVM 应用框架](https://zhuanlan.zhihu.com/p/23772285)



### 组件化

* 知识点
  * **模块化与组件化**

    模块化以功能来区分，在AS中就是一个Module。模块之间会存在一些耦合，组件就是来解决耦合的，可以独立开发、调式、发布。

  * **组件化方案**

    ![图片](https://mmbiz.qpic.cn/mmbiz_png/MOu2ZNAwZwPvHTwqNWpcIToBXzvNG0cYJfet6WvMu8UicPnqBShhon1pgCiaYO7giacAoa6Eq3JiaVlmCV9eWqFvZw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

    组件独立调试可以有两种方案：

    1. 单工程方案，组件以module形式存在，动态配置组件的工程类型；
    2. 多工程方案，业务组件以library module形式存在于独立的工程，且只有这一个library module。每个项目把AAR上传到Maven仓库

    

  * **组件化路由（跳转、通信、解耦） - 以Arouter举例**

    1. 引入依赖 

       一般习惯的做法是把arouter-api的依赖放在基础服务的module里面，因为既然用到了组件化，那么肯定是所有的module都需要依赖arouter-api库的，而arouter-compiler的依赖需要放到每一个module里面。

    2. Arouter初始化 

       ```java
       ARouter.init(mApplication); // 尽可能早，推荐在Application中初始化
       ```

       另外，官方实现了Gradle插件路由的自动加载功能

    3. 添加注解

       1）Route注解添加Activity的路由

       2）Route注解添加全局序列化方式

       3）Route注解定义了全局降级策略

       4）Route注解实现提服务  --- 组件之间通信使用

       5）@Interceptor注解  --- 跳转拦截

    4. 发起路由

       ```java
       ARouter.getInstance().build("/test/1")
                   .withLong("key1", 666L)
                   .withString("key3", "888")
                   .withObject("key4", new Test("Jack", "Rose"))
                   .navigation()
       ```

    5. 混淆

    6. 使用Gradle实现路由表自动加载

       主要是在编译期通过gradle插装把需要依赖arouter注解的类自动扫描到arouter的map管理器里面，而传统的是通过扫描dex文件来过滤arouter注解类来添加到map中

    7. 使用IDE插件通过导航的形式到目标类

    

  * Arouter的源码分析

    1. 编译时干的事 - 生成中间代码

       如下图:

       ![类加载过程](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/daf5b7eac7a7429790bc7239a3bfa9ba~tplv-k3u1fbpfcp-watermark.awebp)

       所以，当我们在gralde中添加了Arouter的依赖后，那么在**编译时**就会 在对应module的 **/build/generated/source/kapt/debug/** 下生成 **"com.alibaba.android.arouter.routes"** 目录，Arouter生成的代码都放在这里，比如:

       ```java
       // 这一个IRouteRoot，看名字"ARouter$$Root$$app"，其中"ARouter$$Root"是前缀，"app"是group名字，也就是path里面以"/"分隔得到的第一个字符串，然后通过"$$"连接，
       // 那么这玩意儿的完整类名就是"com.alibaba.android.arouter.routes.ARouter$$Root$$app"
       public class ARouter$$Root$$app implements IRouteRoot {
       
           // 参数是一个map，value类型是 IRouteGroup
           @Override
           public void loadInto(Map<String, Class<? extends IRouteGroup>> routes) {
               // "app"就是@Route(path = "/app/activity_main") 中的"app"，在Arouter中叫做group，是以path中的"/"分隔得到的
               // 这个的value是:ARouter$$Group$$app.class，也就是下面的类
               routes.put("app", ARouter$$Group$$app.class);
           }
       }
       
       // 这是一个IRouteGroup，同理，前缀是"ARouter$$Group"
       public class ARouter$$Group$$app implements IRouteGroup {
           @Override
           public void loadInto(Map<String, RouteMeta> atlas) {
               // "app/activity_main"就是我们通过@Route指定的path，后面RouteMeta保存了要启动的组件类型，以及对应的.class文件
               // 这个 RouteMeta.build()的参数很重要，后面要用到
               atlas.put("/app/activity_main", RouteMeta.build(RouteType.ACTIVITY, MainActivity.class, "/app/activity_main", "app", null, -1, -2147483648));
           }
       }
       
       // RouteMeta.build()方法，参数后面有用
       // type就是: RouteType.ACTIVITY，
       // destination就是MainActivity.class，
       // path就是"/app/activity_main",
       // group就是"app"
       // paramsType是null
       // priority是-1
       // extra是-2147483648
       public static RouteMeta build(RouteType type, Class<?> destination, String path, String group, Map<String, Integer> paramsType, int priority, int extra) {
           return new RouteMeta(type, null, destination, null, path, group, paramsType, priority, extra);
       }
       ```

       我们发现生成的.java文件，都有个共同的前缀"ARouter$$"，比如"ARouter$$Root"。又因为它们是在Arouter生成的目录下面，所以它们的完整类名都有个前缀:**"com.alibaba.android.arouter.routes.ARouter$$"**。

       

    2. 运行时干的事 - 注入

       1 在Application的onCreate()里面我们调用了Arouter.init(this)。

       2 接着调用了ClassUtils.getFileNameByPackageNam()来获取所有"com.alibaba.android.arouter.routes"目录下的dex文件的路径。

       3 然后遍历这些dex文件获取所有的calss文件的完整类名。

       4 然后遍历所有类名，获取指定前缀的类，然后通过反射调用它们的loadInto(map)方法，这是个注入的过程，都注入到参数Warehouse的成员变量里面了。

       5 其中就有Arouter在编译时生成的"com.alibaba.android.arouter.routes.ARouter$$Root.ARouter$$Root$$app"类，它对应的代码:<"app", ARouter$$Group$$app.class>就被添加到Warehouse.groupsIndex里面了。

       ![image-20210125141700750](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7fcdf181df6241539deaa85c9511b404~tplv-k3u1fbpfcp-watermark.awebp)
       
       
       
    3. 调用时干的事 - 获取
       调用`build`方法会创建一个`Postcard`对象，`Postcard`会根据传入的`Path`从`Warehouse`对象获取对应的对应的`RouteMeta`对象，然后找到对应的`Class`类，封装成Intent，调用系统的`startActivity()`进行跳转。
    
       ![image-20210125141823505](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fb40a4e98e764130a6a551511f9133c8~tplv-k3u1fbpfcp-watermark.awebp)



* 参考资料
  
  * [“终于懂了” 系列：Android组件化，全面掌握！](https://mp.weixin.qq.com/s/WSzpJXXocajJjmWgYem3fA)
  * [阿里 ARouter 全面解析，总有你没了解的](https://mp.weixin.qq.com/s/LaEbPaIKu0TNR0ygI-yOSQ)
  * [ARouter源码分析](https://juejin.cn/post/6921580887616274439#heading-0)
  * [Arouter从使用到原理](https://juejin.cn/post/6995136681850437662#heading-5)
  * [探索 ARouter 原理](https://juejin.cn/post/6885932290615509000#heading-52)



### 插件化

* 知识点 - 看此文章即可 [浅谈Android插件化](https://juejin.cn/post/6973888932572315678#heading-0)
  * 插件的难点

    * 如何加载并执行插件 `*Apk*` *中的代码（*`*ClassLoader Injection*`*）*
    * 让系统能调用插件 `*Apk*` *中的组件（*`*Runtime Container*`*）*
    * 正确识别插件 `*Apk*` *中的资源（*`*Resource Injection*`*）*

  * ClassLoader原理

    * Java中的ClassLoader

      BootstrapClassLoader 负责加载 JVM 运行时的核心类，比如 JAVA_HOME/lib/rt.jar 等等

      ExtensionClassLoader 负责加载 JVM 的扩展类，比如 JAVA_HOME/lib/ext 下面的 jar 包

      AppClassLoader 负责加载 classpath 里的 jar 包和目录

      

    * Android中的ClassLoader

      PathClassLoader 用来加载系统类和应用程序类，可以加载已经安装的 apk 目录下的 dex 文件。

      DexClassLoader 用来加载 dex 文件，可以从存储空间加载 dex 文件。

      

    * 双亲委托机制

      每一个 ClassLoader 中都有一个 parent 对象，代表的是父类加载器，在加载一个类的时候，会先使用父类加载器去加载，如果在父类加载器中没有找到，自己再进行加载，如果 parent 为空，那么就用系统类加载器来加载。通过这样的机制可以保证系统类都是由系统类加载器加载的。

      <img src="https://s2.ax1x.com/2019/05/29/VnM5ZT.png" alt="VnM5ZT.png" style="zoom:80%;" />

      

  * Runtime Container

    * 运行时容器技术

      预埋一些空的 `Android` 组件，使用空组件调用插件的组件。启动插件组件需要依赖容器，容器负责加载插件组件并且完成双向转发，转发来自系统的生命周期回调至插件组件，同时转发来自插件组件的系统调用至系统。

      

    * 字节码替换

      ASM字节码插桩的方式来批量替换插件中的原生组件

      

  * Resource Injection

    * 有两个方法可以获取到插件的Resource资源

      ```java
      PackageManager#getPackageArchiveInfo： 根据 Apk 路径解析一个未安装的 Apk 的 PackageInfo
      PackageManager#getResourcesForApplication： 根据 ApplicationInfo 创建一个 Resources 实例
      ```

      

    * 宿主资源与插件资源merge

      ```java
      public class PluginResources extends Resources {
          private Resources hostResources;
          private Resources injectResources;
      
          public PluginResources(Resources hostResources, Resources injectResources) {
              super(injectResources.getAssets(), injectResources.getDisplayMetrics(), injectResources.getConfiguration());
              this.hostResources = hostResources;
              this.injectResources = injectResources;
          }
      
          @Override
          public String getString(int id, Object... formatArgs) throws NotFoundException {
              try {
                  return injectResources.getString(id, formatArgs);
              } catch (NotFoundException e) {
                  return hostResources.getString(id, formatArgs);
              }
          }
      
          // ...
      }
      ```

      

  * 插件化框架

    * VirtualAPK （滴滴）
    * Shadow
* 参考资料
  * [浅谈Android插件化](https://juejin.cn/post/6973888932572315678#heading-0)
  * [Android解析ClassLoader（一）Java中的ClassLoader](https://juejin.cn/post/6844903498291625992)
  * [Android解析ClassLoader（二）Android中的ClassLoader](http://liuwangshu.cn/application/classloader/2-android-classloader.html)



### 热修复



### ASM 技术 & APT技术（Annotation Processing Tool）

* 概念：ASM是一种通用Java字节码操作和分析框架。它可以用于修改现有的class文件或动态生成class文件。





## 9. APP性能优化 & 稳定性



### 综合知识

![android-performance.png](https://github.com/JsonChao/Awesome-Android-Performance/blob/master/screenshots/android-performance.png?raw=true)

* 参考资料
  * [Awesome-Android-Performance](https://github.com/JsonChao/Awesome-Android-Performance)
  * [分享一波 Android 性能优化的总结！](https://mp.weixin.qq.com/s/MfIT_sV0xwt1UnGQWXCJIA)
  * [性能与功耗 - Android官网](https://developer.android.com/topic/performance)





### 启动速度优化

* 知识点

  * 3种启动状态

    * 热启动

      `热启动是三种启动状态中是最快的一种`，因为热启动是从后台切到了前台，应用的 Activity 还驻留在内存中，应用不需要重复执行对象初始化操作，不需要再创建 Applicaiton，也不需要再进行渲染布局等操作。

    * 暖启动

      `暖启动只会重走 Activity 的生命周期`，启动速度介于冷启动和热启动之间，暖启动只会重走 Activity 的生命周期，不需要重新创建进程和 Application。

    * 冷启动

      `冷启动经历了创建进程、启动应用和绘制界面一系列流程，是耗时最多的，也是常见的启动优化的衡量标准`，一般在线上进行的启动优化都是以冷启动速度为指标的。

  *  3个启动问题

    * 点击图标响应慢
    * 首页显示慢
    * 显示后无法操作

  * 2种测量方法

    1. 命令测量

       打开终端，输入 `adb shell am start -W packagename/首屏 Activity` 打开我们要测量的应用，打开后系统会输出应用的启动时间，`-W` 选项表示等待启动完成。

    2. 埋点测量（代码打点 或 AOP）

       `埋点测量可以精确控制开始和结束的位置而且可以带到线上`，使用埋点测量进行用户数据的采集，可以很方便地带到线上，把数据上报给服务器，服务器可以针对所有用户上报的启动数据，每天做一个整合，计算出一个平均值，然后对比不同版本的启动速度。

  * 2种分析工具

    1. TraceView
       * 第一种：代码中添加：**Debug.startMethodTracing()、检测方法、Debug.stopMethodTracing()**。（需要**使用adb pull将生成的**.trace文件导出到电脑，然后使用Android Studio的Profiler进行加载）
       * 第二种：打开 **Profiler  ->  CPU   ->    点击 Record   ->  点击 Stop  ->  查看Profiler下方Top Down/Bottom Up 区域**，以找出**耗时的热点方法**。
    2. Systrace

  * 4种优化方法

    1. 闪屏页

       使用Activity的windowBackground主题属性预先设置一个启动图片（layer-list实现），在启动后，在Activity的onCreate()方法中的super.onCreate()前再setTheme(R.style.AppTheme)。

    2. 异步初始化

    3. 延迟初始化

    4. 改进优化方案 - 启动器

       启动器的核心思想是充分利用多核 CPU ，自动梳理任务顺序。

    

* 参考资料
  
  * [探索 Android 启动优化方法](https://juejin.cn/post/6844903919580086280)
  * [深入探索Android启动速度优化（上）](https://juejin.cn/post/6844904093786308622)
  * [深入探索Android启动速度优化（下）](https://juejin.cn/post/6870457006784774152)





### UI渲染优化/绘制优化

* 知识点

  * Android常用的绘制优化工具一般有如下几种：

    - Hierarchy View：查看Layout层次

    - Android Studio自带的Profile CPU工具

    - 静态代码检查工具Lint

    - Profile GPU Rendering 

      从Android M版本开始，GPU Profiling工具把渲染操作拆解成如下8个详细的步骤进行显示。

      

      ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2017/2/16/3327d0618ded9ecf441548c12819bf7e~tplv-t2oaga2asx-watermark.awebp)渲染八步骤

      

      > 1. Swap Buffers：表示处理任务的时间，也可以说是CPU等待GPU完成任务的时间，线条越高，表示GPU做的事情越多；
      > 2. Command Issue：表示执行任务的时间，这部分主要是Android进行2D渲染显示列表的时间，为了将内容绘制到屏幕上，Android需要使用Open GL ES的API接口来绘制显示列表，红色线条越高表示需要绘制的视图更多；
      > 3. Sync & Upload：表示的是准备当前界面上有待绘制的图片所耗费的时间，为了减少该段区域的执行时间，我们可以减少屏幕上的图片数量或者是缩小图片的大小；
      > 4. Draw：表示测量和绘制视图列表所需要的时间，蓝色线条越高表示每一帧需要更新很多视图，或者View的onDraw方法中做了耗时操作；
      > 5. Measure/Layout：表示布局的onMeasure与onLayout所花费的时间，一旦时间过长，就需要仔细检查自己的布局是不是存在严重的性能问题；
      > 6. Animation：表示计算执行动画所需要花费的时间，包含的动画有ObjectAnimator，ViewPropertyAnimator，Transition等等。一旦这里的执行时间过长，就需要检查是不是使用了非官方的动画工具或者是检查动画执行的过程中是不是触发了读写操作等等；
      > 7. Input Handling：表示系统处理输入事件所耗费的时间，粗略等于对事件处理方法所执行的时间。一旦执行时间过长，意味着在处理用户的输入事件的地方执行了复杂的操作；
      > 8. Misc Time/Vsync Delay：表示在主线程执行了太多的任务，导致UI渲染跟不上vSync的信号而出现掉帧的情况；出现该线条的时候，可以在Log中看到这样的日志：

      

      ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2017/2/17/e12aea48b89572a35de339652db8b827~tplv-t2oaga2asx-watermark.awebp)
      
      
      
    - TraceView
    
      
    
    - Systrace

  * 布局优化方式

    * 检查工具：Hierarchy View
    * 减少布局嵌套
    * 使用标签（merge、ViewStub、include）

  * 避免过度绘制

    * 检查工具：开发者模式 - 打开调试绘制
    * 移除背景、自定义View优化

  * 合理刷新机制

    * 控制刷新频率
    * 避免没有必要的刷新
    * 缩小刷新区域

  * 提升动画性能

  * **通用套路**

    1. 调试GPU过度绘制，将Overdraw降低到合理范围内；
    2. 减少嵌套层次及控件个数，保持view的树形结构尽量扁平（使用Hierarchy Viewer可以方便的查看），同时移除所有不需要渲染的view；
    3. 使用GPU配置渲染工具，定位出问题发生在具体哪个步骤，使用TraceView精准定位代码；
    4. 使用标签，Merge减少嵌套层次、ViewStub延迟初始化。

    

* 参考资料

  * [Android 性能优化（二）之布局优化面面观](https://juejin.cn/post/6844903463894122509#heading-0)
  * [Android性能优化之绘制优化](https://juejin.cn/post/6844904080989487118#heading-0)



### 内存知识与优化

* 知识点
  * **内存优化**

    * 为什么要做内存优化

      1. 降低crash率  2. 运行更流畅  3. 存活时间长

    * Dalvik和ART

      1. Dalvik 是 Dalvik Virtual Machine（Dalvik 虚拟机）的简称，是 Android 平台的核心组成部分之一，Dalvik 与 JVM 的区别有如下几个。

         > 1）**Dalvik基于寄存器**，JVM基于的是栈
         >
         > 2）**dx工具**。`Dalvik 有自己的字节码`， Dalvik 会用 dx 工具将所有的 .class 文件转换为一个 .dex 文件，然后会从该 .dex 文件读取指令和数据。生成.dex之后，会 通过dexopt工具进行优化生成odex文件。
         >
         > 3）**与 Zygote 共享内存区域**。 Dalvik 由 Zygote 孵化器创建的，Zygote 本身也是一个 Dalvik VM 进程，当系统需要创建一个进程时，Zygote 就会进行 fork，快速创建和初始化一个 DVM 实例。
         >
         > 4） **独立进程空间**。在 Androd 中，每一个应用都运行在一个 Dalvik VM 实例中，`每一个 Dalvik VM 都运行在一个独立的进程空间`，这种机制使得 Dalvik 能在有限的内存中同时运行多个进程。
         >
         > 5）**类共享机制**。Dalvik 拥有预加载—共享机制，不同应用之间在运行时可以共享相同的类，拥有更高的效率。
         >
         > 6）**不兼容JVM**

         ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5cd88e6a2a524d81ad4012ecf875e796~tplv-k3u1fbpfcp-zoom-1.image)

      2. 查看Dalvik堆信息。可以通过`   adb shell getprop dalvik.vm.heapsize` 来获取 设备中每一个进程能够使用的堆大小。

         > 1） 堆分配的初始值。dalvik.vm.heapstartsize 是堆分配的初始值大小，这个值越小，系统内存消耗越慢，但是当应用扩展这个堆，导致 GC 和堆调整时，应用会变慢。
         >
         > 2）单个应用可用最大内存。dalvik.vm.heapgrowthlimit 是单个应用可用最大内存，如果在清单文件中声明 largeHeap 为 true，则 App 使用的内存到 heapsize 才会 OOM，否则达到 heapgrowthlimit 就会 OOM。
         >
         > 3）堆内存最大值。 dalvik.vm.heapsize 是进程可用的堆内存最大值，一旦应用申请的内存超过这个值，就会 OOM。

      3. ART

         ART 的全称是 Android Runtime，是从 Android 4.4 开始新增的**应用运行时环境**，是一个执行本地机器指令的虚拟机，用于替代 Dalvik 虚拟机。ART与Dalvik的区别：

         > 1）预编译。在 ART 中，系统在安装应用时会进行一次预编译（AOT，Ahead-Of-Time），将字节码预先编译成机器码并存储在本地，这样应用就不用在每次运行时执行编译了，运行效率也大大提高。Dalvik 中的应用每次运行时，字节码都需要通过即时编译器 JIT 转换为机器码，这会使得应用的运行效率降低。
         >
         > 
         >
         > 2）垃圾回收算法。在 ART 下，GC 速度比 Dalvik 要快，这是因为应用本身做了垃圾回收的一些工作，启动 GC 后，不再是两次暂停，而是一次暂停，而且 ART 使用了一种新技术（packard pre-cleaning），在暂停前做了许多事情，减轻了暂停时的工作量。在 Dalvik 采用的垃圾回收算法是标记-清除算法，启动垃圾回收机制会造成两次暂停（一次在遍历阶段，另一次在标记阶段）。
         >
         > 
         >
         > 3）64位。Dalvik 是为 32 位 CPU 设计的，而 ART 支持 64 位并兼容 32 位 CPU，这也是 Dalvik 被淘汰的主要原因。

    * 虚拟机内存结构

      1. [运行时的数据区域 | 内存结构](../Java语言知识体系/Java知识体系.md/#运行时的数据区域 | 内存结构)

    * 垃圾回收机制

      1. 可达性分析算法

         > 可达性分析算法（Rechability Analysis）用于判定对象是否存活，这个算法的基本思路就是通过一系列称为 `GC Roots` 的跟对象作为起始节点集，从这些节点开始，根据引用关系向下搜索，搜索过程中所走过的路径称为引用链（Reference Chain），如果某个对象到 GC Roots 之间没有任何引用链相连，也就是不可达时，则证明该对象不可能再被使用。
         >
         > 
         >
         > GC Roots 对象包括下面几种：
         >
         > - 在虚拟机栈（栈帧中的本地变量表）中引用的对象，比如各个线程被调用的方法堆栈中使用到的参数、局部变量和临时变量等
         > - 在方法区中类静态属性引用的对象，比如字符串常量池里的引用
         > - 在本地方法栈中 JNI（Native 方法）引用的对象
         > - Java 虚拟机内部的引用，如基本数据类型对应的 Class 对象
         > - 所有被同步锁（synchronized关键字）持有的对象
         > - 反映 Java 虚拟机内部情况的 JMXBean、JVMTI 中注册的回调和本地代码缓存等

         <img src="https://s3.jpg.cm/2021/06/11/ILjuP6.png" style="zoom:50%;" />

      2. 四种引用类型

         > 在 JDK 1.2 后，Java 对引用的概念进行了扩充，把引用分为`强引用（Strongly Reference）`、`软引用（Soft Reference）`、`弱引用（Weak Reference）`和`虚引用（Phanton Reference）` 4 种。

      3. 分代回收算法

         > 分代回收（Generational Collection）算法把 Java 堆划分为不同的区域，然后把回收对象按照年龄（对象熬过垃圾回收的次数）分配到不同的区域中，这样垃圾回收器就可以每次只回收其中一个或几个区域，所以才有 `Minor GC（Young GC）新生代回收`、`Major GC（Old GC）老年代回收` 和 `Full GC（整个 Java 堆和方法区的垃圾回收）` 这样的回收类型划分，才能针对不同的区域安排与里面存储对象存亡特征匹配的垃圾回收算法，从而发展出了`标记-复制算法`、`标记-清除算法`、`标记-整理算法`等针对性的垃圾回收算法。
         >
         > 根据分代收集理论，Java 堆至少划分为`新生代（Young Generation）`、`老年代（Old Generation）`两个区域，在新生代中，每次垃圾回收时都有大批对象死去，回收后存活的对象会晋升为老年代中存放。

    * 图片对内存有什么影响

    * 什么是内存泄露

      1. 内存泄漏指的是一块内存没有被使用且无法被 GC 回收，从而造成了内存的浪费。

      2. 常见的导致内存泄漏的 3 个原因分别是：`非静态内部类`、`静态变量`、`资源未释放`，`内存泄漏的本质就是`长生命周期对象持有了短生命周期对象的引用，导致短生命周期对象无法被释放。

         > 资源未释放大致为：
         >
         > 忘了注销 BroadcastReceiver
         >
         > 忘了关闭数据库游标（Cursor）
         >
         > 忘了关闭流
         >
         > 忘了调用 recycle() 方法回收创建的 Bitmap 使用的内存
         >
         > 忘了在 Activity 退出时取消 RxJava 或协程所开启异步任务
         >
         > Webview

    * 什么是内存抖动

      1. 当我们在短时间内频繁创建大量临时对象时，就会引起内存抖动

      2. 如何避免

         > - 尽量避免在循环体中创建对象
         > - 尽量不要在自定义 View 的 onDraw() 方法中创建对象，因为这个方法会被频繁调用
         > - 对于能够复用的对象，可以考虑使用对象池把它们缓存起来

  * 内存优化方法

    * Memory profile ([使用内存性能分析器查看应用的内存使用情况](https://developer.android.google.cn/studio/profile/memory-profiler?hl=zh-cn))

      > 内存计数中的类别如下：
      >
      > - **Java**：从 Java 或 Kotlin 代码分配的对象的内存。
      >
      > - **Native**：从 C 或 C++ 代码分配的对象的内存。
      >
      >   即使您的应用中不使用 C++，您也可能会看到此处使用了一些原生内存，因为即使您编写的代码采用 Java 或 Kotlin 语言，Android 框架仍使用原生内存代表您处理各种任务，如处理图像资源和其他图形。
      >
      > - **Graphics**：图形缓冲区队列为向屏幕显示像素（包括 GL 表面、GL 纹理等等）所使用的内存。（请注意，这是与 CPU 共享的内存，不是 GPU 专用内存。）
      >
      > - **Stack**：您的应用中的原生堆栈和 Java 堆栈使用的内存。这通常与您的应用运行多少线程有关。
      >
      > - **Code**：您的应用用于处理代码和资源（如 dex 字节码、经过优化或编译的 dex 代码、.so 库和字体）的内存。
      >
      > - **Others**：您的应用使用的系统不确定如何分类的内存。
      >
      > - **Allocated**：您的应用分配的 Java/Kotlin 对象数。此数字没有计入 C 或 C++ 中分配的对象。
      >
      >   如果连接到搭载 Android 7.1 及更低版本的设备，只有在内存性能分析器连接到您运行的应用时，才开始此分配计数。因此，您开始分析之前分配的任何对象都不会被计入。但是，Android 8.0 及更高版本附带一个设备内置性能剖析工具，该工具可跟踪所有分配，因此，在 Android 8.0 及更高版本上，此数字始终表示您的应用中待处理的 Java 对象总数。

    * MAT

    * LeakCanary

      1. LeakCanary 原理 ，详情 [LeakCanary原理](#LeakCanary)

         > 1）检测保留的实例
         >
         > 2） 堆转储
         >
         > 3）泄漏踪迹
         >
         > 4）泄漏分组

      2. 安装 LeakCanary

      3. 使用 LeakCanary 分析内存泄漏

    * 监听和获取系统内存状态

      1. Android 应用可以通过在 Activity 中实现 ComponentCallback2 接口获取系统内存的相关事件. ComponentCallnback2 提供了 onTrimMemory(level) 回调方法
      2. ActivityManager.getMemoryInfo()。 Android 提供了一个 ActivityManager.getMemoryInfo() 方法给我们查询内存信息，这个方法会返回一个 ActivityManager.MemoryInfo 对象，这个对象包含了系统当前内存状态，这些状态信息包括可用内存、总内存以及低杀内存阈值。

  * 内存优化技巧

    * 谨慎使用 Service，而是使用JobSchedule或新的WorkManager
    * 选择优化后的数据容器。Android 框架包含几个经过优化的数据容器，包括 `SparseArray`、`SparseBooleanArray` 和 `LongSparseArray`。 
    * 小心代码抽象
    * 使用 protobuf精简版 作为序列化数据
    * 避免内存抖动
    * Apk 瘦身
    * 使用 Dagger2 进行依赖注入
    * 谨慎使用第三方库
* 参考资料
  * [探索 Android 内存优化方法](https://juejin.cn/post/6844903897958449166)
  * [Android内存管理知识百科](https://mp.weixin.qq.com/s/LCN3ACxbvtGLiahqokYxTw)
  * [Android 强、软、弱、虚引用 区别和使用场景](https://mp.weixin.qq.com/s/h5MzWRsfRTrrH4z3QIrSzQ)
  * [看完这篇 LeakCanary 原理分析，又可以虐面试官了！](https://mp.weixin.qq.com/s/1jFY_22hoWgCw3CDo2rpOA)





### 体积优化

* 知识点

  * 体积过大对APP性能的影响

    1）、**安装时间**：比如 **文件拷贝、Library 解压，并且，在编译 ODEX 的时候，特别是对于 Android 5.0 和 6.0 系统来说，耗费的时间比较久，而 Android 7.0 之后有了 混合编译，所以还可以接受。最后，App 变大后，其 签名校验 的时间也会变长**。

    2）、**运行时内存：Resource 资源、Library 以及 Dex 类加载都会占用应用的一部分内存**。

    3）、**ROM 空间**：如果应用的安装包大小为 **50MB**，那么启动解压之后很可能就已经超过 **100MB** 了。并且，如果 **闪存空间不足，很可能出现“写入放大”的情况**，它是闪存和固态硬盘（SSD）中一种不良的现象，闪存在可重新写入数据前必须先擦除，而擦除操作的粒度与写入操作相比低得多，执行这些操作就会多次移动（或改写）用户数据和元数据。因此，要改写数据，就需要读取闪存某些已使用的部分，更新它们，并写入到新的位置，如果新位置在之前已被使用过，还需连同先擦除；**由于闪存的这种工作方式，必须擦除改写的闪存部分比新数据实际需要的大得多。即最终可能导致实际写入的物理资料量是写入资料量的多倍**。

    

  * APK的组成

    我们都知道，**Android** 项目最终会编译成一个 **.apk** 后缀的文件，实际上它就是一个 **压缩包**。因此，它内部还有很多不同类型的文件，这些文件，按照大小，共分为如下四类：

    - 1）、**代码相关**：**classes.dex**，我们在项目中所编写的 **java** 文件，经过编译之后会生成一个 **.class** 文件，而这些所有的 **.class** 文件呢，它最终会经过 **dx** 工具编译生成一个 **classes.dex**。
    - 2）、**资源相关**：**res**、**assets**、编译后的二进制资源文件 **resources.arsc** 和 清单文件 等等。**res** 和 **assets** 的不同在于 **res** 目录下的文件会在 **.R** 文件中生成对应的资源 **ID**，而 **assets** 不会自动生成对应的 **ID**，而是通过 **AssetManager** 类的接口来获取。此外，每当在 **res** 文件夹下放一个文件时，**aapt** 就会自动生成对应的 **id** 并保存在 **.R** 文件中，**但 .R 文件仅仅只是保证编译程序不会报错，实际上在应用运行时，系统会根据 ID 寻找对应的资源路径，而 resources.arsc 文件就是用来记录这些 ID 和 资源文件位置对应关系 的文件**。
    - 3）、**So 相关**：**lib** 目录下的文件，这块文件的优化空间其实非常大。

    此外，还有 **META-INF**，它存放了应用的 **签名信息**，其中主要有 **3个文件**，如下所示：

    - 1）、**MANIFEST.MF**：其中每一个资源文件都有一个对应的 **SHA-256-Digest（SHA1)** 签名，**MANIFEST.MF** 文件的 **SHA256（SHA1）** 经过 **base64** 编码的结果即为 **CERT.SF** 中的 **SHA256（SHA1）-Digest-Manifest** 值。
    - 2）、**CERT.SF**：除了开头处定义的 **SHA256（SHA1）-Digest-Manifest** 值，后面几项的值是对 **MANIFEST.MF** 文件中的每项再次 **SHA256（SHA1）** 经过 **base64** 编码后的值。
    - 3）、**CERT.RSA：其中包含了公钥、加密算法等信息。首先，对前一步生成的 CERT.SF 使用了 SHA256（SHA1）生成了数字摘要并使用了 RSA 加密，接着，利用了开发者私钥进行签名。然后，在安装时使用公钥解密。最后，将其与未加密的摘要信息（MANIFEST.MF文件）进行对比，如果相符，则表明内容没有被修改。**

    

  * APK瘦身方案

    * 代码瘦身方案探索

      1. **ProGuard**

         混淆器的 **作用** 不仅仅是 **保护代码**，它也有 **精简编译后程序大小** 的作用，其 **通过缩短变量和函数名以及丢失部分无用信息等方式，能使得应用包体积减小**。

         

         在 **Android SDK** 里面集成了一个工具 — **Proguard**，它是一个免费的 **Java** 类文件 **压缩、优化、混淆、预先校验** 的工具。它的 **主要作用** 大概可以概括为 **两点**，如下所示：

         - 1）、**瘦身：它可以检测并移除未使用到的类、方法、字段以及指令、冗余代码，并能够对字节码进行深度优化。最后，它还会将类中的字段、方法、类的名称改成简短无意义的名字**。
         - 2）、**安全：增加代码被反编译的难度，一定程度上保证代码的安全**。

         

         

      2. **D8 与 R8 优化**

         D8 的 **优化效果** 总的来说可以归结为如下 **四点**：

         - 1）、**Dex的编译时间更短**。
         - 2）、**.dex文件更小**。
         - 3）、**D8 编译的 .dex 文件拥有更好的运行时性能**。
         - 4）、**包含 Java 8 语言支持的处理**。

         

         **R8 是 Proguard 压缩与优化部分的替代品**，并且它仍然使用与 **Proguard** 一样的 **keep** 规则。**ProGuard** 和 **R8** 都应用了基本名称混淆：它们 **都使用简短，无意义的名称重命名类，字段和方法**。他们还可以 **删除调试属性**。但是，**R8 在 inline 内联容器类中更有效，并且在删除未使用的类，字段和方法上则更具侵略性**。

         

      3. 去除 debug 信息与行号信息

      4. Dex 分包优化

      5. 使用 XZ Utils 进行 Dex 压缩

      6. **三方库处理**

         1）将三方库进行统一

         2）在选择第三方 **SDK** 的时候，我们可以将包大小作为选择的指标之一，我们应该 **尽可能地选择那些比较小的库来实现相同的功能**

         3）如果我们引入三方库的时候，可以 **只引入部分需要的代码**，而不是将整个包的代码都引入进来。

         

      7. **移除无用代码**

         1）使用 Lint 检测无效代码

         2）移除无用代码。有一个很好的方法可以 **准确地判断哪些类在线上环境下用户肯定不会用到了**。我们可以通过 **AOP** 的方式来做，对于 **Activity** 来说，其实非常简单，我们只需要 **在每个 Activity 的 onCreate 当中加上统计** 即可，然后**到了线上之后，如果这个 Activity 被统计了，就说明它还在被使用**。而对于那些 **不是 Activity 的类**，我们可以 **利用 AOP 来切它们的构造函数**，一个类如果它被使用，那它的构造函数肯定会被调用到。

         

      8. 避免产生 Java access 方法

      9. 利用 ByteX Gradle 插件平台中的代码优化插件

    * 资源瘦身方案探索

      1. **冗余资源优化**

         1.1 使用 Lint 的 Remove Unused Resource

         ​		**APK** 的资源主要包括图片、**XML**，与冗余代码一样，它也可能遗留了很多旧版本当中使用而新版本中不使用的资源，这点在快速开发的 **App** 中更可能出现。**Android Lint 不会分析 assets 文件夹下的资源，因为 assets 文件可以通过文件名直接访问，不需要通过具体的引用，Lint 无法判断资源是否被用到**。

         

         1.2 优化 shrinkResources 流程真正去除无用资源

         ​		shrinkResources 用来开启删除无用资源，也就是没有被引用的文件（经过实测是drawable,layout，实际并不是彻底删除，而是保留文件名，但是没有内容，等等），但是因为需要知道是否被引用所以需要配合mififyEnable使用，只有当两者都为true的时候才会起到真正的删除无效代码和无引用资源的目的

         

      2. **重复资源优化**

         一个 **App** 一般会有多个业务团队进行开发，其中每个业务团队在资源提交时的资源名称可能会有重复的，这将会 **引发资源覆盖的问题**，因此，每个业务团队都会为自己的 **资源文件名添加前缀**。这样就导致了这些资源文件虽然 **内容相同**，但因为 **名称的不同而不能被覆盖**，最终都会被集成到 **APK** 包中。这里，我们还是可以 **在 Android 构建工具执行 package${flavorName}Task 之前通过修改 Compiled Resources 来实现重复资源的去除**，具体放入实现原理可细分为如下三个步骤：

         - 1）、首先，**通过资源包中的每个ZipEntry的CRC-32 checksum来筛选出重复的资源**。
         - 2）、然后，**通过[android-chunk-utils](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fmadisp%2Fandroid-chunk-utils)修改resources.arsc，把这些重复的资源都重定向到同一个文件上**。
         - 3）、最后，**把其它重复的资源文件从资源包中删除，仅保留第一份资源**。

         

      3. 图片压缩

         对于图片压缩，我们可以在 [tinypng](https://link.juejin.cn?target=https%3A%2F%2Ftingpng.com%2F) 这个网站进行图片压缩，但是如果 **App** 的图片过多，一个个压缩也是很麻烦的。因此，我们可以 **使用 [McImage](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FsmallSohoSolo%2FMcImage%2Fblob%2Fmaster%2FREADME-CN.md)、[TinyPngPlugin](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FDeemonser%2FTinyPngPlugin) 或 [TinyPIC_Gradle_Plugin](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fmeili%2FTinyPIC_Gradle_Plugin%2Fblob%2Fmaster%2FREADME.zh-cn.md) 来对图片进行自动化批量压缩**。但是，需要注意的是，**在 Android 的构建流程中，AAPT 会使用内置的压缩算法来优化 res/drawable/ 目录下的 PNG 图片，但这可能会导致本来已经优化过的图片体积变大**，因此，可以通过在 **build.gradle** 中 **设置 cruncherEnabled 来禁止 AAPT 来优化 PNG 图片**，代码如下所示：

         ```
         aaptOptions {
             cruncherEnabled = false
         }
         ```

         

      4. **使用针对性的图片格式**

         。首先，**如果能用 VectorDrawable 来表示的话，则优先使用 VectorDrawable；否则，看是否支持 WebP，支持则优先用 WebP；如果也不能使用 WebP，则优先使用 PNG，而 PNG 主要用在展示透明或者简单的图片，对于其它场景可以使用 JPG 格式**。简单来说可以归结为如下套路：

         ```
         VD（纯色icon）->WebP（非纯色icon）->Png（更好效果） ->jpg（若无alpha通道）
         ```

         用 **图形化** 的形式如下所示：

         ![image](https:////p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a6eaa9bb40544b4ca512e336ad6384f2~tplv-k3u1fbpfcp-watermark.awebp)

         使用矢量图片之后，它能够有效的减少应用中图片所占用的大小，**矢量图形在 Android 中表示为 VectorDrawable 对象**。它 **仅仅需100字节的文件即可以生成屏幕大小的清晰图像**，但是，**Android 系统渲染每个 VectorDrawable 对象需要大量的时间，而较大的图像需要更长的时间**。 因此，建议 **只有在显示纯色小 icon 时才考虑使用矢量图形**。（我们可以利用这个 [在线工具](https://link.juejin.cn?target=http%3A%2F%2Finloop.github.io%2Fsvg2android%2F) 将矢量图转换成 VectorDrawable）。

         最后，如果要在项目中使用 VD，则以下几点需要着重注意：

         - 1）、**必须通过 app:arcCompat 属性来使用 svg，如果通过 src，则在低版本手机上会出现不兼容的问题**。

         - 2）、可能会**不兼容selector**，在 **Activity** 中手动兼容即可，兼容代码如下所示：

           ```java
            static {                           
                AppCompatDelegate.setCompatVectorFromResourcesEnabled(true)    
            }
           ```

           

         - 3）、**不兼容第三方库**。

         - 4）、**性能问题：当Vector比较简单时，效率肯定比Bitmap高，复杂则效率会不如Bitmap**。

         - 5）、**不便于管理：建议原则为同目录多类型文件，以前缀区别，不同目录相同类型文件，以意义区分**。

         

      5. **资源混淆**

         同代码混淆类似，资源混淆将 **资源路径混淆成单个资源的路径**，这里我们可以使用 **AndroidResGuard**，它可以使冗余的资源路径变短，例如将 **res/drawable/wechat** 变为 **r/d/a**。

         [AndroidResGuard 项目地址](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fshwenzhang%2FAndResGuard%2Fblob%2Fmaster%2FREADME.zh-cn.md)

         

      6. **R Field 的内联优化**

         我们可以通过内联 **R Field** 来进一步对代码进行瘦身，此外，它也**解决了 R Field 过多导致 MultiDex 65536 的问题**。要想实现内联 **R Field**，我们需要 **通过 Javassist 或者 ASM 字节码工具在构建流程中内联 R Field**

         

         可以 **直接使用蘑菇街的 [ThinRPlugin](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fmeili%2FThinRPlugin%2Fblob%2Fmaster%2FREADME.zh-cn.md)**。它的实现原理为：**android 中的 R 文件，除了 styleable 类型外，所有字段都是 int 型变量/常量，且在运行期间都不会改变。所以可以在编译时，记录 R 中所有字段名称及对应值，然后利用 ASM 工具遍历所有 Class，将除 R$styleable.class 以外的所有 R.class 删除掉，并且在引用的地方替换成对应的常量**，从而达到缩减包大小和减少 **Dex** 个数的效果。此外，最近 **ByteX** 也增加了 [shrink_r_class](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fbytedance%2FByteX%2Fblob%2Fmaster%2Fshrink-r-plugin%2FREADME-zh.md) 的 **gradle** 插件，它不仅可以在编译阶段对 **R** 文件常量进行内联，而且还可以 **针对 App 中无用 Resource 和无用 assets 的资源进行检查**。
         
         
         
      7. 资源合并方案

         我们可以把所有的资源文件合并成一个大文件，而 **一个大资源文件就相当于换肤方案中的一套皮肤**。它的效果 **比资源混淆的效果会更好**，但是，在此之前，必须要解决 **解析资源** 与 **管理资源** 的问题。

         

      8. **资源文件最少化配置**

         我们需要 **根据 App 目前所支持的语言版本去选用合适的语言资源**，例如使用了 **AppCompat**，如果不做任何配置的话，最终 **APK** 包中会包含 **AppCompat** 中所有已翻译语言字符串，无论应用的其余部分是否翻译为同一语言。对此，我们可以 **通过 resConfig 来配置使用哪些语言，从而让构建工具移除指定语言之外的所有资源**。同理，也可以**使用 resConfigs 去配置你应用需要的图片资源文件类，如 "xhdpi"、"xxhdpi" 等等**，代码如下所示：

         ```
               android {
                   ...
                   defaultConfig {
               	    ...
                       resConfigs "zh", "zh-rCN"
                       resConfigs "nodpi", "hdpi", "xhdpi", "xxhdpi", "xxxhdpi"
                   }
                   ...
               }    
               复制代码
         ```

         此外，我们还以 **利用 Density Splits 来选择应用应兼容的屏幕尺寸大小**，代码如下所示：

         ```
               android {
                   ...
                   splits {
                       density {
                           enable true
                           exclude "ldpi", "tvdpi", "xxxhdpi"
                           compatibleScreens 'small', 'normal', 'large', 'xlarge'
                       }
                   }
                   ...
               }
         ```

         

      9. **尽量每张图片只保留一份**

         比如说，我们统一只把图片放到 **xhdpi** 这个目录下，那么 **在不同的分辨率下它会做自动的适配**，即 **等比例地拉伸或者是缩小**。

      10. **资源在线化**

          可以 **将一些图片资源放在服务器**，然后 **结合图片预加载** 的技术手段，这些 **既可以满足产品的需要，同时可以减小包大小**。

      11. **统一应用风格**

          如设定统一的 **字体、尺寸、颜色和按钮按压效果、分割线 shape、selector 背景** 等等。



* So 瘦身方案探索
  
  1. So 移除方案
    
     。理论上来说，**对应架构的 CPU 它的执行效率是最高的**，但是这样会导致 **在 lib 目录下会多存放了各个平台架构的 So 文件**，所以 **App** 的体积自然也就更大了。
    
     因此，我们就需要对 **lib** 目录进行缩减，我们 **在 build.gradle 中配置这个 abiFiliters 去设置 App 支持的 So 架构**，其配置代码如下所示：
    
     ```
         defaultConfig {
             ndk {
                 abiFilters "armeabi"
             }
         }
         复制代码
     ```
    
     **一般情况下，应用都不需要用到 neon 指令集，我们只需留下 armeabi 目录就可以了**。因为 **armeabi 目录下的 So 可以兼容别的平台上的 So**，相当于是一个万金油，都可以使用。但是，这样 **别的平台使用时性能上就会有所损耗，失去了对特定平台的优化**。
    
     
    
  2. So 移除方案优化版
    
     上面我们说到了想要完美支持所有类型的设备代价太大，那么，我们能不能采取一个 **折中的方案**，就是 **对于性能敏感的模块，它使用到的 So，我们都放在 armeabi 目录当中随着 Apk 发出去，然后我们在代码中来判断一下当前设备所属的 CPU 类型，根据不同设备 CPU 类型来加载对应架构的 So 文件**。这里我们举一个小栗子，比如说我们 **armeabi** 目录下也加上了 **armeabi-v7** 对应的 **So**，然后我们就可以在代码当中做判断，如果你是 **armeabi-v7** 架构的手机，那我们就直接加载这个 **So**，以此达到最佳的性能，这样包体积其实也没有增加多少，同时也实现了高性能的目的，比如 **微信和腾讯视频 App** 里面就使用了这种方式，如下图所示：
    
     ![image](https:////p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/95614c97a6d141509abd4b9ac5de3f06~tplv-k3u1fbpfcp-watermark.awebp)
    
     看到上图中的 **libimagepipeline_x86.so**，下面我们就以这个 so 为例来写写加载它的伪代码，如下所示：
    
     ```
         String abi = "";
         // 获取当前手机的CPU架构类型
         if (Build.VERSION.SDK_INT < Build.VERSION_CODES.LOLLIPOP) {
             abi = Buildl.CPU_ABI;
         } else {
             abi = Build.SUPPORTED_ABIS[0];
         }
         
         if (TextUtils.equals(abi, "x86")) {
             // 加载特定平台的So
             
         } else {
             // 正常加载
             
         }
     ```
    
     
    
  3. 使用 XZ Utils 对 Native Library 进行压缩
    
  4. 对 Native Library 进行合并
    
  5. 删除 Native Library 中无用的导出 symbol
    
  6. So 动态下载
    
     可以 **将部分 So 文件使用动态下发的形式进行加载**。也就是在业务代码操作之前，我们可以先从服务器下载下来 So，接下来再使用，这样包体积肯定会减少不小。但是，如果要把这项技术 **稳定落地到实际生产项目中需要解决一些问题**，具体的 so  动态化关键技术点和需要避免的坑可以参见 [动态下发 so 库在 Android APK 安装包瘦身方面的应用 ](https://link.juejin.cn?target=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FX58fK02imnNkvUMFt23OAg)。
    
     
  
* 其它优化方案

  1. 插件化
  2. 业务梳理
  3. 转变开发模式

* 六、包体积监控
  
  1）、**大小监控：通常是记录当前版本与上一个或几个版本直接的变化情况，如果当前版本体积增长较大，则需要分析具体原因，看是否有优化空间**。
  
  2）、**依赖监控：包括J ar、aar 依赖**。
  
  3）、**规则监控：我们可以把包体积的监控抽象为无用资源、大文件、重复文件、R  文件等这些规则**。
  
  
  
* 七、瘦身优化常见问题

  1. 怎么降低 Apk 包大小？

     1）、**代码：Proguard、统一三方库、无用代码删除**。

     2）、**资源：无用资源删除、资源混淆**。

     3）、**So：只保留 Armeabi、更优方案**。

  2. Apk 瘦身如何实现长效治理？

     1）、**发版之前与上个版本包体积对比，超过阈值则必须优化**。

     2）、**推进插件化架构改进**。

* 参考资料
  
  * [深入探索 Android 包体积优化（匠心制作-上）](https://juejin.cn/post/6844904103131234311)
  * [深入探索 Android 包体积优化（匠心制作-下）](https://juejin.cn/post/6872920643797680136#heading-41)
  * [缩减、混淆处理和优化应用](https://developer.android.com/studio/build/shrink-code)
  * [Android机型适配终极篇](https://zhuanlan.zhihu.com/p/92083368)



### 功耗优化

* **知识点**

  * 电量检测的工具

    1. Battery Historian

    2. dumpsys batterystats

       

  * 如何进行电量优化

    * 监控电池电量和充电电量

      根据具体的业务，考虑将一些不需要及时地和用户交互的操作放到充电 的时候去做。

      

      `BatteryManager` 会在一个包含充电状态的粘性 `Intent` 中广播所有电池和充电详情。由于它是一个粘性 Intent，通过简单地调用 `registerReceiver` 传入 `null` 作为接收器来注册 `BroadcastReceiver`，便可返回当前电池状态 Intent。

      ```java
          IntentFilter ifilter = new IntentFilter(Intent.ACTION_BATTERY_CHANGED);
          Intent batteryStatus = context.registerReceiver(null, ifilter);
      ```

      可以提取当前充电状态，并且如果设备正在充电，则还可以提取设备是通过 USB 还是交流充电器进行充电。

      ```java
          // Are we charging / charged?
          int status = batteryStatus.getIntExtra(BatteryManager.EXTRA_STATUS, -1);
          boolean isCharging = status == BatteryManager.BATTERY_STATUS_CHARGING ||
                               status == BatteryManager.BATTERY_STATUS_FULL;
      
          // How are we charging?
          int chargePlug = batteryStatus.getIntExtra(BatteryManager.EXTRA_PLUGGED, -1);
          boolean usbCharge = chargePlug == BatteryManager.BATTERY_PLUGGED_USB;
          boolean acCharge = chargePlug == BatteryManager.BATTERY_PLUGGED_AC;
          
      ```

      监听电池变化，在清单中注册一个 `BroadcastReceiver`，通过在一个 Intent 过滤器内定义 `ACTION_POWER_CONNECTED` 和 `ACTION_POWER_DISCONNECTED` 来同时监听这两种事件。

      ```java
      <receiver android:name=".PowerConnectionReceiver">
            <intent-filter>
              <action android:name="android.intent.action.ACTION_POWER_CONNECTED"/>
              <action android:name="android.intent.action.ACTION_POWER_DISCONNECTED"/>
            </intent-filter>
          </receiver>
      ```

      

    * JobSchedule

      JobScheduler 可以允许开发者在符合某些条件下创造执行在后台的任务，我们可以设置执行一些耗电操作的场景，比如说 处于 WIFI 状态下同时连接电源 的情况下。同时，要注意用户在离开界面后，要避免耗电的操作，比如说停止播放动画。通过这些操作，我们的 App 就不会比之前耗电了。

      

    * WakeLock

      我们在实际项目中使用 WakeLock 有几个注意事项：

      第一，acquire、release 要成对地释放，

      第二，尽量使用 acquire 的超时方法来设置超时时间，避免因为异常情况从而导致 WakeLock 而无法释放的情况，

      第三，关于 WakeLock 的释放一定要写在 try-catch-finally 的 finally 当中，保证 WakeLock 在异常情况下的释放。

      

    * 屏幕唤醒

      根据自己的 APP 实际情况，根据业务来控制好是否保持屏幕常量

      

    * 灭屏时或不在前台时停止动画和刷新

      

    * GPS

      根据场景谨慎选择定位模式：对定位准确度没那么高的场景可以选择低精度模式。

      可以考虑网络定位代替 GPS。

      及时注销定位监听。

      

    * 传感器

      使用传感器，选择合适的采样率，越高的采样率类型则越费电。

      

* **参考资料**

  * [监控电池电量和充电状态](https://developer.android.google.cn/training/monitoring-device-state/battery-monitoring#java)
  * [深入探索 Android 电量优化](https://juejin.cn/post/6844904195523346439#heading-0)
  * [Android电量优化全解析](https://juejin.cn/post/6844903779268034574#heading-0)



### 流畅性优化|卡顿优化

* 知识点

  * 为什么会卡顿？

    **流畅度、响应速度、稳定性**（ANR）这三类之所以用户感知都是卡顿，是因为这三类问题产生的原理是一致的，都是由于主线程的 Message 在执行任务的时候超时，根据不同的超时阈值来进行划分而已。比如在 **滑动列表的时候掉帧**、**应用启动白屏过长**、**点击电源键亮屏慢**、**界面操作没有反应然后闪退**、**点击图标没有响应**、**窗口动画不连贯、滑动不跟手、重启手机进入桌面卡顿** 等场景。

    

  * 如何监控卡顿？

    * 方案一：Looper#loop方法中的 logging.println，计算出处理Message的时间。需要在后台开一个线程，定时获取主线程堆栈，**局限**： 只适合线下。
    * 方案二：通过Gradle Plugin+ASM，编译期在每个方法开始和结束位置分别插入一行代码，统计方法耗时。字节码插桩技术，适合线上。微信Matrix 。
    
  * TraceView

    两种使用方式：

    * 第一种：代码中添加：**Debug.startMethodTracing()、检测方法、Debug.stopMethodTracing()**。（需要**使用adb pull将生成的**.trace文件导出到电脑，然后使用Android Studio的Profiler进行加载）
    * 第二种：打开 **Profiler  ->  CPU   ->    点击 Record   ->  点击 Stop  ->  查看Profiler下方Top Down/Bottom Up 区域**，以找出**耗时的热点方法**。

    

  * Systrace

    * 如何捕捉系统跟踪记录
    
      1. 通过python命令systrace
    
      2. 使用Android Device Monitor
    
      3. System Tracing 的系统级应用，要求 Android 9（API 级别 28）或更高版本的设备，在**开发者选项** -> 调试 中
    
         
    
    * 系统跟踪分析工具
    
      1. Systrace 是平台提供的旧版命令行工具，可记录短时间内的设备活动，并保存在压缩的文本文件中。该工具会生成一份报告，其中汇总了 Android 内核中的数据，例如 CPU 调度程序、磁盘活动和应用线程。
    
      2. Perfetto 是 Android 10 中引入的全新平台级跟踪工具。这是适用于 Android、Linux 和 Chrome 的更加通用和复杂的开源跟踪项目。与 Systrace 不同，它提供数据源超集，可让您以 protobuf 编码的二进制流形式记录任意长度的跟踪记录。
    
      
    
    * 浏览Systrace报告
    
      1. 用户互动
    
      2. CPU活动
    
      3. 系统事件 （SurfaceFlinger、Input事件）
    
      4. 显示帧 （显示线程状态）
    
      5. 检查界面帧和提醒
    
         
    
    * 举例？

* 参考资料

  * [卡顿、ANR、死锁，线上如何监控？](https://juejin.cn/post/6973564044351373326)
  * [《广研Android卡顿监控系统》](https://mp.weixin.qq.com/s/MthGj4AwFPL2JrZ0x1i4fw)
  * [Systrace实战：彻底搞懂卡顿原理！](https://mp.weixin.qq.com/s/eij3z6wlT4k3GGQm637b-A)
  * [Android Systrace 基础知识 -- 分析 Systrace 预备知识](https://www.androidperformance.com/2019/07/23/Android-Systrace-Pre/)





### 稳定性知识与优化

* 知识点

  稳定性从宏观维度来说是包含了崩溃、性能等一些维度的，但是我们这边只从微观角度来看，关于性能、功耗等会放到别的章节里面。那么微观角度会分为Crash和ANR。

  * **Crash（Java和Native）**

    * Java

      

    * Native

      1. so 组成

         一个完整的 so 由C代码加一些 debug 信息组成，这些debug信息会记录 so 中所有方法的对照表，就是方法名和其偏移地址的对应表，也叫做符号表，这种 so  也叫做未 strip 的，通常体积会比较大。

      2. NE的捕获与解析

         1） logcat捕获。获取到logcat日志之后，可以通过使用Android/SDK/NDK下面提供的一个工具ndk-stack，它可以直接将NE输出的log解析为可阅读的日志。

         ​       

         2）通过DropBox日志解析--适用于系统应用。 DropBox会记录JE，NE，ANR的各种日志，只需要将DropBox下面的日志传上来即可进行分析解决。借助ndk-stack工具 或 addr2line工具来解析堆栈。

         

         3）通过BreakPad捕获解析--适用于所有应用。

         BreakPad主要提供两个个功能，NE的监听和回调，生成minidump文件，也就是dmp结尾的文件，另外提供两个工具，符号表工具和堆栈还原工具。

         ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3c88fff16e1941f28fe76b0fcf87a972~tplv-k3u1fbpfcp-watermark.awebp)

         - **符号表工具：**用于从so中提取出debug信息，获取到堆栈对应的符号表。
         - **堆栈还原工具：**用于将BreakPad生成的dump文件还原成符号，也就是堆栈偏移值。

         

         由上述可以得知，BreakPad在应用发生NE崩溃时，可以将NE对应的minidump文件写入到本地，同时会回调给应用层，应用层可以针对本次崩溃做一些处理，达到捕获统计的作用，后续将minidump文件上传之后结合minidump_stackwalk以及addr2line工具可以还原出实际堆栈，示意图如下：

         ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a7fb9897484248be8c4496306cca36ea~tplv-k3u1fbpfcp-watermark.awebp)

         

      

  * **ANR**

    * **ANR的原理**

      看完这三篇文章即可：[彻底理解安卓应用无响应机制](http://gityuan.com/2019/04/06/android-anr/)、 [理解Android ANR的触发原理](http://gityuan.com/2016/07/02/android-anr/)、[Input系统—ANR原理分析](http://gityuan.com/2017/01/01/input-anr/)（同Input输入系统）

      * ANR的触发机制

        Service、Broadcast、Provider本质都是在AMS中通过消息机制来监测是否发生超时并触发ANR。

        Input主要是由于建立连接的Window问题或者是事件队列中的事件来不及处理

      * ANR的触发原理

      * ANR超时阈值

        不同组件的超时阈值各有不同，关于service、broadcast、contentprovider以及input的超时阈值如下表：

        <img src="http://gityuan.com/images/android-anr/anr_timeout.jpg" alt="anr_timeout" style="zoom:80%;" />

      * ANR上报机制（阅读 [理解Android ANR的信息收集过程](http://gityuan.com/2016/12/02/app-not-response/)）

        无论哪种类型的ANR发生以后，最终都会调用 AMS.appNotResponding() 方法。

        

    * **ANR分析法**

      除了主体逻辑，发生ANR时还会输出各种类别的日志：**event log**：通过检索”am_anr”关键字，可以找到发生ANR的应用**main log**：通过检索”ANR in “关键字，可以找到ANR的信息，日志的上下文会包含CPU的使用情况**dropbox**：通过检索”anr”类型，可以找到ANR的信息**traces**：发生ANR时，各进程的函数调用栈信息

      

  * **Android crash机制**

    * 摘录自 [理解Android Crash处理流程](http://gityuan.com/2016/06/24/app-crash/)，本文主要以源码的视角，详细介绍了到应用crash后系统的处理流程：

      1. 首先发生crash所在进程，在创建之初便准备好了defaultUncaughtHandler，用来来处理Uncaught Exception，并输出当前crash基本信息；
      2. 调用当前进程中的AMP.handleApplicationCrash；经过binder ipc机制，传递到system_server进程；
      3. 接下来，进入system_server进程，调用binder服务端执行AMS.handleApplicationCrash；
      4. 从`mProcessNames`查找到目标进程的ProcessRecord对象；并将进程crash信息输出到目录`/data/system/dropbox`；
      5. 执行makeAppCrashingLocked
         - 创建当前用户下的crash应用的error receiver，并忽略当前应用的广播；
         - 停止当前进程中所有activity中的WMS的冻结屏幕消息，并执行相关一些屏幕相关操作；
      6. 再执行handleAppCrashLocked方法，
         - 当1分钟内同一进程`连续crash两次`时，且`非persistent`进程，则直接结束该应用所有activity，并杀死该进程以及同一个进程组下的所有进程。然后再恢复栈顶第一个非finishing状态的activity;
         - 当1分钟内同一进程`连续crash两次`时，且`persistent`进程，，则只执行恢复栈顶第一个非finishing状态的activity;
         - 当1分钟内同一进程`未发生连续crash两次`时，则执行结束栈顶正在运行activity的流程。
      7. 通过mUiHandler发送消息`SHOW_ERROR_MSG`，弹出crash对话框；
      8. 到此，system_server进程执行完成。回到crash进程开始执行杀掉当前进程的操作；
      9. 当crash进程被杀，通过binder死亡通知，告知system_server进程来执行appDiedLocked()；
      10. 最后，执行清理应用相关的activity/service/ContentProvider/receiver组件信息。

      这基本就是整个应用Crash后系统的执行过程。 最后，再说说对于同一个app连续crash的情况：

      - 当60s内连续crash两次的非persistent进程时，被认定为bad进程：那么如果第3次从后台启动该进程(Intent.getFlags来判断)，则会拒绝创建进程；
      - 当crash次数达到两次的非persistent进程时，则再次杀该进程，即便允许自启的service也会在被杀后拒绝再次启动。

      

      **当进程抛出未捕获异常时，则系统会处理该异常并进入crash处理流程：**

      ![app_crash](http://gityuan.com/images/stability/app_crash.jpg)

      **当crash进程执行kill操作后，进程被杀。此时需要掌握binder 死亡通知原理，由于Crash进程中拥有一个Binder服务端`ApplicationThread`，而应用进程在创建过程调用attachApplicationLocked()，从而attach到system_server进程，在system_server进程内有一个`ApplicationThreadProxy`，这是相对应的Binder客户端。当Binder服务端`ApplicationThread`所在进程(即Crash进程)挂掉后，则Binder客户端能收到相应的死亡通知，从而进入binderDied流程。**

      ![binder_died](http://gityuan.com/images/stability/binder_died.jpg)

* 参考资料
  * [深入探索Android稳定性优化](https://juejin.cn/post/6844903972587716621)
  * [彻底理解安卓应用无响应机制](http://gityuan.com/2019/04/06/android-anr/)
  * [卡顿、ANR、死锁监控方案](https://mp.weixin.qq.com/s/EzIbxeWexQv7ENDugT-UFg)
  * [Android NativeCrash 捕获与解析](https://juejin.cn/post/6932757164003950599#heading-0)
  * [理解Android Crash处理流程](http://gityuan.com/2016/06/24/app-crash/)



## 10. Android安全

* 知识点

  

* 参考资料
  * [那些值得你试试的Android竞品分析工具](http://www.jianshu.com/p/ba2d9eca47a2)
  * [使用“aapt dump”查看APK内容](http://blog.sina.com.cn/s/blog_6294abe70101bkef.html)
  * [Android 反编译初探 应用是如何被注入广告的](https://blog.csdn.net/lmj623565791/article/details/53370414)



## 11. Android JNI

* 知识点

  * **so库存放**

    * **ABI是什么以及作用**

      ABI是英文Application Binary Interface的缩写，即应用二进制接口。

      不同Android设备，使用的CPU架构可能不同，因此支持不同的指令集。 CPU 与指令集的每种组合都有其自己的应用二进制接口（或 ABI）,ABI非常精确地定义了应用程序的机器代码应如何在运行时与系统交互。您必须为要与您的应用程序一起使用的每种CPU架构指定一个ABI（Application Binary Interface）。

      ABI 包含以下信息：

      - 可使用的 CPU 指令集（和扩展指令集）。
      - 运行时内存存储和加载的字节顺序。Android 始终是 little-endian。
      - 在应用和系统之间传递数据的规范（包括对齐限制），以及系统调用函数时如何使用堆栈和寄存器。
      - 可执行二进制文件（例如程序和共享库）的格式，以及它们支持的内容类型。Android 始终使用 ELF。
      - 如何重整 C++ 名称。

      Android目前支持以下7种ABIs:

      ```
      mips, mips64, X86, X86–64, arm64-v8a, armeabi, armeabi-v7a
      ```

      

      对于CPU来说，不同的架构并不意味着一定互不兼容，根据目前Android共支持七种不同类型的CPU架构，其兼容特点可总结如下：

      - armeabi设备只兼容armeabi；
      - armeabi-v7a设备兼容armeabi-v7a、armeabi；
      - arm64-v8a设备兼容arm64-v8a、armeabi-v7a、armeabi；
      - X86设备兼容X86、armeabi；
      - X86_64设备兼容X86_64、X86、armeabi；
      - mips64设备兼容mips64、mips；
      - mips只兼容mips；

      根据以上的兼容总结，我们还可以得到一些规律：

      - armeabi的SO文件基本上可以说是万金油，它能运行在除了mips和mips64的设备上，但在非armeabi设备上运行性能还是有所损耗；
      - 64位的CPU架构总能向下兼容其对应的32位指令集，如：x86_64兼容X86，arm64-v8a兼容armeabi-v7a，mips64兼容mips；

      

    * **ABI是如何工作的？**

      * Android 系统在运行时知道它支持哪些 ABI，因为版本特定的系统属性会指示：

        - 设备的主要 ABI，与系统映像本身使用的机器代码对应。
        - （可选）与系统映像也支持的其他 ABI 对应的辅助 ABI。

        此机制确保系统在安装时从软件包提取最佳机器代码。

      ##### 主辅助ABI具体适配流程

      前面说了ABI的工作原理，一个Android设备支持主辅ABI,那么他们具体是如何工作的呢？我们以`arm64-v8a`架构的手机为例：

      <img src="https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/5/3/171dafefbaac1c42~tplv-t2oaga2asx-watermark.awebp" alt="img" style="zoom: 80%;" />

      对于一个cpu是arm64-v8a架构的手机，它运行app时，进入jnilibs去读取库文件时，先看有没有arm64-v8a文件夹，如果没有该文件夹，去找armeabi-v7a文件夹，如果没有，再去找armeabi文件夹，如果连这个文件夹也没有，就抛出异常；

      如果有arm64-v8a文件夹，那么就去找特定名称的.so文件，注意：如果没有找到想要的`.so`文件，不会再往下（armeabi-v7a文件夹）找了，而是直接抛出异常。

      ```
      Exception:Java.lang.UnsatisfiedLinkError: dlopen failed: library “/***.so” not found 
      ```

      特别需要注意的情况是在命中了文件夹，而未命中so文件这种情况：

      - 比如命中了`arm64-v8a`文件夹，没有找到需要的so文件，就不会再往下（armeabi-v7a文件夹）找了，而是直接抛出异常。
      - 如果你的项目用到了第三方依赖，如果只保留一个ABI的时候，建议在Build中加入ndk.abiFilters
      - 例如：第三方aar文件，如果这个sdk对abi的支持比较全，可能会包含armeabi、armeabi-v7a、x86、arm64-v8a、x86_64五种abi，而你应用的其它so只支持armeabi、armeabi-v7a、x86三种，直接引用sdk的aar，会自动编译出支持5种abi的包。但是应用的其它so缺少对其它两种abi的支持，那么如果应用运行于arm64-v8a、x86_64为首选abi的设备上时，就会==crash==了哦。

      因此，我们需要在我们的app中配置 abiFilter 配置，来避免一些未知的错误。

      ```xml
      defaultConfig {  
          ndk {  
              abiFilters "armeabi"// 指定ndk需要兼容的ABI(这样其他依赖包里x86,armeabi,arm-v8之类的so会被过滤掉) 
          }  
      }
      ```
      
      
      
    * 项目中该如何适配
      `Q1`： 只适配了`armeabi-v7a`,那如果APP装在其他架构的手机上，如`arm64-v8a`上，会蹦吗？
    
      `A:` 不会，但是反过来会。
    
      因为`armeabi-v7a`和`arm64-v8a`会向下兼容：
    
      - 只适配`armeabi`的APP可以跑在`armeabi`,`x86`,`x86_64`,`armeabi-v7a`,`arm64-v8`上
      - 只适配`armeabi-v7a`可以运行在`armeabi-v7a`和`arm64-v8a`
      - 只适配`arm64-v8a` 可以运行在`arm64-v8a`上
    
      那我们该如何适配呢？给出如下几个方案：
    
      ```
      方案一`：只适配`armeabi
      ```
    
      - `优点:`基本上适配了全部CPU架构（除了淘汰的mips和mips_64）
      - `缺点：`性能低，相当于在绝大多数手机上都是需要辅助ABI或动态转码来兼容
    
      ```
      方案二`：只适配 `armeabi-v7a
      ```
    
      同理方案一，只是又筛掉了一部分老旧设备,在性能和兼容二者中比较平衡
    
      ```
      方案三:` 只适配 `arm64-v8
      ```
    
      - `优点:` 性能最佳
      - `缺点：` 只能运行在`arm64-v8`上，要放弃部分老旧设备用户
    
      这三种方案都是可以的，现在的大厂APP适配中，这三种都有，大部分是前2种方案。具体选哪一种就看自己的考量了，以性能换兼容就`arm64-v8`,以兼容换性能`armeabi`,二者稍微平衡一点的就`armeabi-v7a`。
      
      
      
    * 性能+兼容能否兼得
      为每个CPU架构单独打一个APK，该apk 中就只包含一个平台，多APK上传。




  * **so库加载和使用**

    Android加载so文件的方式有两种：

    1. System.load。 参数必须为库文件的绝对路径。动态加载。
    2. System.loadLibrary 。 参数为库文件名，不包含库文件的扩展名，必须是在JVM属性Java.library.path所指向的路径中，路径可以通过System.getProperty(‘java.library.path’)获得。静态加载。

    

    从两种加载方式可以分为 静态加载 和 动态加载，静态加载就是把so直接放到apk中，动态加载就是so文件不打包进apk,在安装完应用打开app的时候通过后台下载so库，将下载下来的so文件再写入到app里面。

    

* 参考资料
  
  * [为何大厂APP如微信、支付宝、淘宝、手Q等只适配了armeabi-v7a/armeabi？](https://juejin.im/post/5eae6f86e51d454ddb0b3dc6)
  * [Android - JNI 开发你所需要知道的基础](https://juejin.cn/post/6844904192780271630)
  * [Android SO 文件的兼容和适配](https://juejin.cn/post/6844903477164900365)
  * [Android——JNI加载so两种方式](https://blog.csdn.net/iliupp/article/details/70765891)



## 12. Andorid系统源码分析

### Android 系统启动

* 知识点

  1. **BootLoader**

  2. **Linux Kernel**

     Linux 内核负责初始化各种软硬件环境，加载驱动程序，挂载根文件系统(/)等，最重要的是，内核启动完成后，它会在根文件系统中寻找 ”init” 文件，然后启动 init 进程。

  3. **init 进程**

     创建zygote进程、servicemanager、mediaservice等进程。

  4. **zygote 进程**

     zygote 的主要工作：

     - 创建 java 虚拟机 AndroidRuntime

     - 通过 AndroidRuntime 启动 ZygoteInit 进入 java 环境。

       

     ZygoteInit　的主要工作如下：

     - 创建 socket 服务，接受 ActivityManagerService 的应用启动请求。
     - 加载 Android framework 中的 class、res（drawable、xml信息、strings）到内存。Android 通过在 zygote 创建的时候加载资源，生成信息链接，再有应用启动，fork 子进程和父进程共享信息，不需要重新加载，同时也共享 VM。
     - 启动 SystemServer。
     - 监听 socket，当有启动应用请求到达，fork 生成 App 应用进程。

  5. **SystemServer 进程**

     SystemServer 的主要的作用是启动各种系统服务，比如 ActivityManagerService，PackageManagerService，WindowManagerService 以及硬件相关的 Service 等服务，我们平时熟知的各种系统服务其实都是在 SystemServer 进程中启动的，这些服务都运行在同一进程（即 SystemServer 进程）的不同线程中，而当我们的应用需要使用各种系统服务的时候其实也是通过与 SystemServer 进程通讯获取各种服务对象的句柄进而执行相应的操作的。在所有的服务启动完成后，会调用各服务的 service.systemReady(…) 来通知各对应的服务，系统已经就绪。

  6. **Launcher 的启动**

     SystemServer　进程再启动的过程中会启动PackageManagerService，PackageManagerService启动后会将系统中的应用程序安装完成。SystemServer启动完所有的服务后，会调用各服务的 service.systemReady(…)。Ｌauncher　的启动逻辑就在 ActivityManagerService.systemReady() 中。

  7. **BootAnimation 退出**

  ![android-boot-flow](http://qiushao.net/images/android-boot-flow.png)
* 参考资料
  * [Android系统开发进阶-系统启动流程概要](http://qiushao.net/2020/02/22/Android%E7%B3%BB%E7%BB%9F%E5%BC%80%E5%8F%91%E8%BF%9B%E9%98%B6/Android%E7%B3%BB%E7%BB%9F%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B/)
  * [Android系统启动源码分析](http://blog.csdn.net/ynztlxdeai/article/details/69675754)



### Android 消息机制

* 知识点

  * ThreadLocal<T>

    * 工作原理

      ThreadLocal是一个线程内部的数据存储类，通过它可以在指定的线程中存储数据，数据存储以后，只有在指定线程中可以获取到存储的数据，对于其他线程来说则无法获取到数据。

    * 使用场景

      1. 当某些数据是以线程为作用域并且不同线程具有不同的数据副本的时候，就可以考虑采用ThreadLocal。
      2. 另一个使用场景是复杂逻辑下的对象传递，比如监听器的传递，有些时候一个线程中的任务过于复杂，在这种情况下，我们有需要监听器能够贯穿整个线程的执行过程。此时可以采用ThreadLocal。

    * 内部实现

      1. ThreadLocal是一个泛型，只需要弄清楚 ThreadLocalMap

      2. ThreadLocalMap，在Thread中有一个threadLocals的变量来存储数据。ThreadLocalMap中通过table数组变量进行存储，而table的索引在set的时候会通过算法计算出来。（table默认大小为16）

         * 对于某一ThreadLocal来讲，他的索引值是确定的，在不同线程之间访问时访问的是不同的table数组的同一位置即都为table[i]，只不过这个不同线程之间的table是独立的。
  * 对于同一线程的不同ThreadLocal来讲，这些ThreadLocal实例共享一个table数组，然后每个ThreadLocal实例在table中的索引i是不同的。
         
  ```
             /* ThreadLocal values pertaining to this thread. This map is maintained
              * by the ThreadLocal class. */
             ThreadLocal.ThreadLocalMap threadLocals = null;
  ```
  
  ```
         
         //ThreadLocalMap构造方法
         ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
                 //内部成员数组，INITIAL_CAPACITY值为16的常量
                 table = new Entry[INITIAL_CAPACITY];
                 //位运算，结果与取模相同，计算出需要存放的位置
                 //threadLocalHashCode比较有趣
                 int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
                 table[i] = new Entry(firstKey, firstValue);
                 size = 1;
                 setThreshold(INITIAL_CAPACITY);
         }
  ```
  

  * 消息队列MessageQueue的工作原理

    * MessageQueue主要包含两个操作：插入和读取。读取操作会伴随插入操作，分别对应的方法为 enqueueMessage 和 next。

    * MessageQueue 使用单链表的数据结构来维护消息列表。

    * next方法是一个无限循环的方法，如果消息队列中没有消息，那么n ext方法会一直阻塞在这里。当有新消息到来时，next方法会返回这条消息并将其从单链表中移除。

    * nativePollOnce的底层逻辑

      1. Linux eventfd事件等待/响应机制
      2. Linux IO多路复用epoll ，
      3.  epoll_wait 读取消息 和 wake 唤醒

      <img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e1f07df30c95445888836dfcf7f158f6~tplv-k3u1fbpfcp-watermark.awebp" style="zoom: 33%;" />

    

  * Looper的工作原理

    * Looper扮演着消息循环的角色，就是不停地从MessageQueue中查看是否有新消息，如果有新消息就会立即处理，否则就一直阻塞在那里。

    * Looper在构造方法中它会创建一个MessageQueue，然后将当前线程的对象保存起来。

      ```java
          private Looper(boolean quitAllowed) {
              mQueue = new MessageQueue(quitAllowed);
              mThread = Thread.currentThread();
          }
      ```

    * Looper.prepare()为当前线程创建一个Looper， Looper.loop()开始消息循环。

      ```java
      class LooperThread extends Thread {
      
          public Handler mHandler;
      
          public void run() {
      
              //将当前线程初始化为Looper线程
              Looper.prepare();
      
              // ...其他处理，如实例化handler
              mHandler = new Handler() {
                  public void handleMessage(Message msg) {
                      // process incoming messages here
                  }
              };
      
              // 开始循环处理消息队列
              Looper.loop();
          }
      }
      ```

    * Looper.prepareMainLooper ，主要是给主线程也是ActivityThread创建Looper使用的，其本质也是通过prepare方法来实现的。

      Looper.getMainLooper方法来获取主线程的Looper

    * quit和quitSafely 退出一个Looper。区别是：quit是直接退出Looper，而quitSafely只是设定一个退出标记，把已有消息处理完成后安全退出。只有Looper推出后线程才会停止，所以不用的Looper一定要退出。

    * Looper.loop方法是一个死循环，只有当MessageQueue的next返回了null才会跳出循环，而当调用了Looper的quit或quitSafely的时候会直接调用MessageQueue的quit，从而让next返回null。

    * Looper消息处理方式：MessageQueue.next返回信息后，`msg.target.dispatchMessage(msg);` 中 msg.target就是Handler对象，发送消息最终又交给了Handler的dispathMessage。

  * Handler的工作原理

    * Handler的工作主要包含消息的发送和接收。send和post两种方式。

    * Handler的dispatchMessage处理消息的流程：

      1. 首先，检查Message的callback是否为null，此callback时一个Runnable对象，实际上是Handler的post方法传递的Runnable参数。，
      2. 其次，检查mCallback时候是否为null，不为null就调用mCallback的handleMessage方法来处理消息。此Callback是在创建Handler对象的时候可以作为构造函数的参数来进行赋值，作用是用来创建一个Handler的实例单并不需要派生Handler的子类。
      3. 最后，调用handleMessage。

      ```java
          /**
           * Handle system messages here.
           */
          public void dispatchMessage(@NonNull Message msg) {
              if (msg.callback != null) {
                  handleCallback(msg);
              } else {
                  if (mCallback != null) {
                      if (mCallback.handleMessage(msg)) {
                          return;
                      }
                  }
                  handleMessage(msg);
              }
          }
      ```

    * IdleHandler 

      1. IdleHandler 说白了，就是 Handler 机制提供的一种可以在 Looper 事件循环的过程中，当出现空闲的时候，允许我们执行任务的一种机制。
      
      2. IdleHandler 被定义在 MessageQueue 中，它是一个接口。在 MessageQueue 中定义了对应的 add 和 remove 方法。add 或 remove 其实操作的都是 `mIdleHandlers`，它的类型是一个 ArrayList。
      
      3. IdleHandler 主要是在 MessageQueue 出现空闲的时候被执行，那么何时出现空闲？
      
         > 1. MessageQueue 为空，没有消息；
         > 2. MessageQueue 中最近需要处理的消息，是一个延迟消息（when>currentTime），需要滞后执行；
      
      4. 在 `next()` 中，当队列空闲的时候，循环 mIdleHandler 中记录的 IdleHandler 对象，如果其 `queueIdle()` 返回值为 `false` 时，将其从 `mIdleHander` 中移除。
      
      

  * Message的工作原理

    * Message 有 `public` 修饰的构造函数，但是一般不建议直接通过构造函数来构建 Message，而是通过 `Message.obtain()` 来获取消息，从而达到Message的复用效果。

      `sPool` 是消息缓存池，链表结构，其最大容量 `MAX_POOL_SIZE` 为 50。`obtain()` 方法会直接从消息池中取消息，循环利用，节约资源。当消息池为空时，再去新建消息。

      ```java
      public static Message obtain() {
          synchronized (sPoolSync) {
              if (sPool != null) {
                  Message m = sPool;
                  sPool = m.next;
                  m.next = null;
                  m.flags = 0; // clear in-use flag
                  sPoolSize--;
                  return m;
              }
          }
          return new Message();
      }
      ```

    * `msg.recycleUnchecked() ` 这个方法会回收已经分发处理的消息，并放入缓存池中。

      ```java
      void recycleUnchecked() {
          // Mark the message as in use while it remains in the recycled object pool.
          // Clear out all other details.
          flags = FLAG_IN_USE;
          what = 0;
          arg1 = 0;
          arg2 = 0;
          obj = null;
          replyTo = null;
          sendingUid = -1;
          when = 0;
          target = null;
          callback = null;
          data = null;
      
          synchronized (sPoolSync) {
              if (sPoolSize < MAX_POOL_SIZE) {
                  next = sPool;
                  sPool = this;
                  sPoolSize++;
              }
          }
      }
      ```

      

  * 主线程的消息循环

    * ActivityThread的主入口为main，会通过Looper.prepareMainLooper()创建主线程的Looper以及MessageQueue，并通过Looper.loop来开启主线程的循环。
    * ActivityThread.H 为对应的Handler，主要包含四大组件的启动和停止等过程。

  * 在子线程中更新UI

    1. Handler 和 Message
    2. runOnUIThread
    3. View.post()

* 参考资料

  * 《Android开发艺术探索》
  * [ThreadLocal](https://www.jianshu.com/p/3c5d7f09dfbd)
  * [Android消息机制2-Handler(Native层)](http://gityuan.com/2015/12/27/handler-message-native/)
  *  [Android基础进阶 - 消息机制 之Native层分析](https://juejin.cn/post/6973142800808280071#heading-0)
  * [面试官：“看你简历上写熟悉 Handler 机制，那聊聊 IdleHandler 吧？”](https://juejin.cn/post/6844904068129751047)




### Android Input输入系统

* 知识点
  * **Input输入系统**
  
    * **输入事件传递流程的组成部分**
  
      1. 输入事件传递流程可以大致的分为三个部分，分别是输入系统部分、WMS处理部分和View处理部分。
  
      <img src="https://s2.ax1x.com/2019/05/27/VZh4FU.png" style="zoom:67%;" />
  
      2. 输入系统部分，主要又分为输入子系统和InputManagerService组成（以下简称IMS），输入事件所产生的原始信息会被Linux内核中的输入子系统采集，原始信息由Kernel space的驱动层一直传递到User space的设备节点。IMS所做的工作就是监听/dev/input下的所有的设备节点，当设备节点有数据时会将数据进行加工处理并找到合适的Window，将输入事件派发给它。
  
         <img src="https://s2.ax1x.com/2019/05/27/VZhIW4.png" style="zoom: 67%;" />
  
    * **IMS创建**
  
      SystemServcie的startOtherServices中初始化InputManagerService  ->  创建InputManagerHandler（使用android.display线程） 并调用 nativeInit函数   ->  在nativeInit创建InputManager
  
      ```java
       private void startOtherServices() {
       ...
                 inputManager = new InputManagerService(context);//1
                 traceEnd();
                 traceBeginAndSlog("StartWindowManagerService");
                 // WMS needs sensor service ready
                 ConcurrentUtils.waitForFutureNoInterrupt(mSensorServiceStart, START_SENSOR_SERVICE);
                 mSensorServiceStart = null;
                 wm = WindowManagerService.main(context, inputManager,
                         mFactoryTestMode != FactoryTest.FACTORY_TEST_LOW_LEVEL,
                         !mFirstBoot, mOnlyCore, new PhoneWindowManager());//2
                 ServiceManager.addService(Context.WINDOW_SERVICE, wm);
                 ServiceManager.addService(Context.INPUT_SERVICE, inputManager);
                 traceEnd();
      ...           
      
      }
      ```
  
      InputManager构造函数中创建了InputReader和InputDispatcher，InputReader会不断循环读取EventHub中的原始输入事件，将这些原始输入事件进行加工后交由InputDispatcher，InputDispatcher中保存了WMS中的所有Window信息（WMS会将窗口的信息实时的更新到InputDispatcher中），这样InputDispatcher就可以将输入事件派发给合适的Window。InputReader和InputDispatcher都是耗时操作，因此在initialize函数中创建了供它们运行的线程InputReaderThread和InputDispatcherThread。
  
      ![](https://s2.ax1x.com/2019/05/27/VZh5YF.png)
  
    * **IMS的启动和Input事件的处理**
  
      四个关键的类，分别是IMS、EventHub、InputDispatcher和InputReader，它们做了如下的工作：
  
      1. IMS启动了InputDispatcherThread和InputReaderThread，分别用来运行InputDispatcher和InputReader。
  
      2. InputDispatcher先于InputReader被创建，InputDispatcher的dispatchOnceInnerLocked函数用来将事件分发给合适的Window。InputDispatcher没有输入事件处理时会进入睡眠状态，等待InputReader通知唤醒。
  
      3. InputReader通过EventHub的getEvents函数获取事件信息，如果是原始输入事件，就将这些原始输入事件交由不同的InputMapper来处理，最终交由InputDispatcher来进行分发。
  
      4. InputReader的notifyKey函数中会根据按键数据来判断InputDispatcher是否要被唤醒，InputDispatcher被唤醒后，会重新调用dispatchOnceInnerLocked函数将输入事件分发给合适的Window。
  
         ![](https://s2.ax1x.com/2019/05/27/VZ4ac9.png)
  
    * **输入事件分发到窗口**
  
      ![](https://s2.ax1x.com/2019/05/27/VZHju4.png)
  
      1. Motion事件在InputReaderThread线程中的InputReader进行加工，加工完毕后会判断是否要唤醒InputDispatcherThread，如果需要唤醒，会在InputDispatcherThread的线程循环中不断的用InputDispatcher来分发 Motion事件。
  
      2. 将Motion事件交由InputFilter过滤，如果返回值为false，这次Motion事件就会被忽略掉。
  
      3. InputReader对Motion事件加工后的数据结构为NotifyMotionArgs，在InputDispatcher的notifyMotion函数中，用NotifyMotionArgs中的事件参数信息构造一个MotionEntry对象。这个MotionEntry对象会被添加到InputDispatcher的mInboundQueue队列的末尾。
  
      4. 如果mInboundQueue不为空，取出mInboundQueue队列头部的EventEntry赋值给mPendingEvent。
  
      5. 根据mPendingEvent的值，进行事件丢弃处理。
  
      6. 调用InputDispatcher的findTouchedWindowTargetsLocked函数，在mWindowHandles窗口列表中为Motion事件找到目标窗口，并为该窗口生成inputTarget。
  
      7. 根据inputTarget获取一个Connection，依赖Connection将输入事件发送给目标窗口。
  
         
  
    * **InputChannel**
  
      InputChannel是什么时候创建的，其实应用在setView()的是会调用IWindowSession的addToDisplay()函数。
      IWindowSession的具体实现在\services\core\java\com\android\server\wm\Session.java中。它包含了InputChannel的建立。它本身是个binder调用，服务端是WMS，最终调用的是WMS的addWindow方法。InputChannel通过InputChannel.openInputChannelPair分别创建一对InputChannel，然后将Server端的InputChannel注册到InputDispatcher中，将Client端的InputChannel返回给客户端应用。InputChannel数组的一对InputChannel，一个注册给了InputDispatcher，另一个交给应用程序ViewRootImpl。
  
      ![](https://upload-images.jianshu.io/upload_images/2828107-dcbf781f1b97a7a9.png?imageMogr2/auto-orient/strip|imageView2/2/w/726/format/webp)
  
    
  
  * **View的事件体系**
* 参考资料
  * [Android输入系统（一）输入事件传递流程和InputManagerService的诞生](https://juejin.cn/post/6844903703200137223)
  * [Android输入系统（二）IMS的启动过程和输入事件的处理](https://juejin.cn/post/6844903717574017038)
  * [Android输入系统（三）InputReader的加工类型和InputDispatcher的分发过程](http://liuwangshu.cn/framework/ims/3-inputdispatcher.html)
  * [Android输入系统（四）输入事件是如何分发到Window的？](https://juejin.cn/post/6844903761131880455)
  * [Android Input（五）-InputChannel通信](https://www.jianshu.com/p/b09afd403f71)
  * [Android4.1 InputManagerService 流程 - 自己总结](https://blog.csdn.net/Siobhan/article/details/8014246)
  * [可能是讲解Android事件分发最好的文章](https://cloud.tencent.com/developer/article/1179381)



### Android AMS系统分析

* 知识点
  * Application 应用启动流程

    1. 点击Launcher桌面的App图标

    2. AMS发起socket请求
    3. Zygote进程接收请求并处理参数
    4. Zygote进程fork出应用进程，应用进程继承得到虚拟机实例
    5. 应用进程启动binder线程池、运行ActivityThread类的main函数、启动Looper循环

    ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6631644fc5c7402cb5363a5a65e8f009~tplv-k3u1fbpfcp-watermark.awebp)

    

    * 参考资料
      * [图解 | 一图摸清Android应用进程的启动](https://juejin.cn/post/6887431834041483271)

  * Activity扮演的角色（作用）

  * Activity的启动流程

    * 启动流程可以用一下的图来解释

      ![Android复习总结 —— Activity启动流程](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/30c91006923a4d848c8adbb2cfb34478~tplv-k3u1fbpfcp-watermark.awebp)

      几个基础概念：

      > - **ActivityThread**：
      >
      > App进程的实际入口。在`main()`方法中，会开启主线程`Handler`的`Looper`消息循环，处理系统消息。
      >
      > - **ApplicationThread** ：
      >
      > 与系统进程进行Binder通讯的`IApplicationThread`代理类。主要用于`system_server`进程对App进程的Binder代理调用，管理生命周期。
      >
      > - **Instrumentation**：
      >
      > 每个App进程唯一的生命周期管理具体执行类。Activity、Application的生命周期管理都是由该类通过`callXXX()`系列方法来实现的。
      >
      > - **ActivityTaskManagerService**：
      >
      > 用于管理活动及其容器(任务、堆栈、显示……)的系统服务。接受App进程发送的**请求启动Activity**的Binder代理调用。
      >
      > - **ActivityManagerService**：
      >
      > 在系统进程中管理Activity与其他组件运行状态的系统服务。接受App进程发送**绑定Application**的Binder代理调用。
      >
      > - **ActivityStarter**：
      >
      > 在系统进程中管理如何启动Activity的控制器
      >
      > - **ActivityStack**：
      >
      > Activity所在的回退栈。控制Activity的回退顺序，在栈顶就是当前获取到焦点的Activity。
      >
      > - **ActivityStackSupervisor**：
      >
      > 系统内唯一，所有回退栈**ActivityStack**的管理类。
      >
      > - **ClientLifecycleManager**：
      >
      > 向App进程通过Binder发送具体生命周期状态事务的管理类。

      

      大致流程：

      > 1. `startActivity()`通过Binder通信向**system_service进程**的`ActivityManagerService`通知需要启动Activity
      > 2. **system_service进程**会先将当前处于焦点状态的`Activity`迁移至**onPause状态**。（Binder方式）
      > 3. 根据`Activity`**启动模式**，启动目标`Activity`。（**如果是进程已存在则跳过4、5**）
      > 4. 如果新Activity所在的进程不存在于系统内，会通过Socket消息通知**Zygote进程**。以**Zygote进程**为原型**fork**拷贝出新的进程，并以反射的方式调用`ActivityThread.main`方法。
      > 5. `ActivityThread.attach`方法内通过Binder通知**system_service进程**的`ActivityManagerService`对新进程进行绑定与初始化。然后`ActivityManagerService`再通知新进程创建`Application`并执行`onCreate`方法。
      > 6. 进程创建并启动完成后，调用`realStartActivityLocked`创建并启动`Activity`，通过Binder通知目标`Activity`所在进程执行生命周期事务，并最终调用`Activity.onCreate`方法，完成启动事务。

      

    

    * 参考资料
      * [Android复习总结 —— Activity启动流程](https://juejin.cn/post/6897451019177820173)
      * [Android深入四大组件（六）Android8.0 根Activity启动过程（前篇）](http://liuwangshu.cn/framework/component/6-activity-start-1.html)
      * [Android深入四大组件（七）Android8.0 根Activity启动过程（后篇）](http://liuwangshu.cn/framework/component/7-activity-start-2.html)

    

  * Service的启动流程

    * startService的流程

      ![](https://img-blog.csdnimg.cn/20200108145130415.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoaXNoaTE5OTQzMw==,size_16,color_FFFFFF,t_70)

    * binderService的流程

      ![img](http://gityuan.com/images/ams/bind_service.jpg)

    * 参考资料

      * [基于Android10的Service的启动流程](https://blog.csdn.net/chishi199433/article/details/103891219)
      * [Service的启动流程——基于Android11](https://blog.csdn.net/qq_26498311/article/details/116980167)
  
  * Broadcast的启动流程

    * 广播注册小结

      1. 传递的参数为广播接收者BroadcastReceiver和Intent过滤条件IntentFilter；
    2. 创建对象LoadedApk.ReceiverDispatcher.InnerReceiver，该对象继承于IIntentReceiver.Stub；
      3. 通过AMS把当前进程的ApplicationThread和InnerReceiver对象的代理类，注册登记到system_server进程；
    4. 当广播receiver没有注册过，则创建广播接收者队列ReceiverList，该对象继承于ArrayList， 并添加到AMS.mRegisteredReceivers(已注册广播队列)；
      5. 创建BroadcastFilter，并添加到AMS.mReceiverResolver；
    6. 将BroadcastFilter添加到该广播接收者的ReceiverList
  
    * 发送广播过程的流程如下

      ![](https://skytoby.github.io/2019/BroadcastCast%E5%B9%BF%E6%92%AD%E6%9C%BA%E5%88%B6%E5%8E%9F%E7%90%86/broadcast.jpg)

    * **广播机制**
  
    1.当发送串行广播（order= true）时
  
    - 静态注册的广播接收者（receivers），采用串行处理
      - 动态注册的广播接收者（registeredReceivers），采用串行处理

      2.当发送并行广播（order= false）时

      - 静态注册的广播接收者（receivers），采用串行处理
    - 动态注册的广播接收者（registeredReceivers），采用并行处理
  
      静态注册的receiver都是采用串行处理；动态注册的registeredReceivers处理方式无论是串行还是并行，取决于广播的发送方式（processNextBroadcast）；静态注册的广播由于其所在的进程没有创建，而进程的创建需要耗费系统的资源比较多，所以让静态注册的广播串行化，防止瞬间启动大量的进程。
  
      
  
      广播ANR只有在串行广播时才需要考虑，因为接收者是串行处理的，前一个receiver处理慢，会影响后一个receiver；并行广播通过一个循环一次性将所有的receiver分发完，不存在彼此影响的问题，没有广播超时。
      
      
      
      串行超时情况：1. 某个广播处理时间>2 * receiver总个数 * mTimeoutPeriod，其中mTimeoutPeriod，前台队列为10s,后台队列为60s；
      
      ​                           2. 某个receiver的执行时间超过mTimeoutPeriod。
      
    * 参考资料
  
      * [BroadcastCast广播机制原理](https://skytoby.github.io/2019/BroadcastCast%E5%B9%BF%E6%92%AD%E6%9C%BA%E5%88%B6%E5%8E%9F%E7%90%86/)
  
  * Provider的启动流程
  
    * 参考资料
      * [Android10.0 ContentProvider原理分析](https://skytoby.github.io/2019/ContentProvider%E5%8E%9F%E7%90%86%E5%88%86%E6%9E%90/)
* 参考资料
  * [[译]Android Application启动流程分析](https://www.jianshu.com/p/a5532ecc8377)
  * [Android应用程序进程启动过程（前篇）](http://liuwangshu.cn/framework/applicationprocess/1.html)
  * [Android应用程序进程启动过程（后篇）](http://liuwangshu.cn/framework/applicationprocess/2.html)
  * [不怕面试再问 Activity，一次彻底地梳理清楚！](https://mp.weixin.qq.com/s/FdfBfyePoX2BI5OkXtmICA)



### Android WMS系统分析

* 知识点

  * **Activity 的 Window的添加过程**

    ActivityThread.performLaunchActivity  -> 创建Activity，调用 Activity.attach -> 创建 PhoneWindow -> Activity.setContentView ->  创建 DecorView ->  ActivityThread.handleResumeActivity -> WindowManagerImpl.addView  -> WindowManagerGlobal.addView -> 创建ViewRootImpl -> ViewRootImpl.setView -> IWindowSession.addtoDisplay

    ![](https://upload-images.jianshu.io/upload_images/9696036-b817d3db3ce31ee5.png?imageMogr2/auto-orient/strip|imageView2/2/w/762/format/webp)

  * WindowManagerService解析

    * WMS概述

      WMS是系统的其他服务，无论对于应用开发还是Framework开发都是重点的知识，它的职责有很多，主要有以下几点：

      #### **窗口管理**

      WMS是窗口的管理者，它负责窗口的启动、添加和删除，另外窗口的大小和层级也是由WMS进行管理的。窗口管理的核心成员有DisplayContent、WindowToken和WindowState。

      #### **窗口动画**

      窗口间进行切换时，使用窗口动画可以显得更炫一些，窗口动画由WMS的动画子系统来负责，动画子系统的管理者为WindowAnimator。

      #### **输入系统的中转站**

      通过对窗口的触摸从而产生触摸事件，InputManagerService（IMS）会对触摸事件进行处理，它会寻找一个最合适的窗口来处理触摸反馈信息，WMS是窗口的管理者，因此，WMS“理所应当”的成为了输入系统的中转站。

      #### **Surface管理**

      窗口并不具备有绘制的功能，因此每个窗口都需要有一块Surface来供自己绘制。为每个窗口分配Surface是由WMS来完成的。

      WMS的职责可以简单总结为下图。

      ![](https://s2.ax1x.com/2019/05/28/VexIGd.png)

    * WMS启动

      SystemServer中startOtherServices  ->  DisplayThread的getHandler来初始化WMS对象 ， 说明WMS的创建是运行在“android.display”线程中  -> 在WMS的构造函数中初始化WindowManagerPolicy（PhoneWindowManager）。

      

      “system_server”线程中会调用WMS的main方法，main方法中会创建WMS，创建WMS的过程运行在”android.display”线程中，它的优先级更高一些，因此要等创建WMS完毕后才会唤醒处于等待状态的”system_server”线程。
      WMS初始化时会执行initPolicy方法，initPolicy方法会调用PWM的init方法，这个init方法运行在”android.ui”线程，并且优先级更高，因此要先执行完PWM的init方法后，才会唤醒处于等待状态的”android.display”线程。
      PWM的init方法执行完毕后会接着执行运行在”system_server”线程的代码，比如本文前部分提到WMS的
      systemReady方法。

      ![](https://s2.ax1x.com/2019/05/28/VexoRA.png)

    * WMS中的重要成员

      ```java
      final WindowManagerPolicy mPolicy;
      final IActivityManager mActivityManager;
      final ActivityManagerInternal mAmInternal;
      final AppOpsManager mAppOps;
      final DisplaySettings mDisplaySettings;
      ...
      final ArraySet<Session> mSessions = new ArraySet<>();
      final WindowHashMap mWindowMap = new WindowHashMap();
      final ArrayList<AppWindowToken> mFinishedStarting = new ArrayList<>();
      final ArrayList<AppWindowToken> mFinishedEarlyAnim = new ArrayList<>();
      final ArrayList<AppWindowToken> mWindowReplacementTimeouts = new ArrayList<>();
      final ArrayList<WindowState> mResizingWindows = new ArrayList<>();
      final ArrayList<WindowState> mPendingRemove = new ArrayList<>();
      WindowState[] mPendingRemoveTmp = new WindowState[20];
      final ArrayList<WindowState> mDestroySurface = new ArrayList<>();
      final ArrayList<WindowState> mDestroyPreservedSurface = new ArrayList<>();
      ...
      final H mH = new H();
      ...
      final WindowAnimator mAnimator;
      ...
       final InputManagerService mInputManager
      ```

      1. **mPolicy：WindowManagerPolicy**
      2. **mSessions：ArraySet**
      3. **mWindowMap：WindowHashMap**
      4. **mFinishedStarting：ArrayList**
      5. **mResizingWindows：ArrayList**
      6. **mAnimator：WindowAnimator**
      7. **mH：H**
      8. **mInputManager：InputManagerService**

      

      ![img](https://skytoby.github.io/2019/WMS%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B%E5%88%86%E6%9E%90/WMS.jpg)

    

    * 在WMS中添加Window的过程

       请求WMS添加Window，mWindowManager.addView(mview, wmParams)   ->  WindowManagerImpl.addView  -> WindowManagerGlobal.addView -> 创建ViewRootImpl ->  ViewRootImpl.setView ->  IWindowSession.addtoDisplay（ViewRootImpl中有一个W变量，为Window的服务端会传递到WMS的WindowState中，用于WMS来通知APP）->  WMS.addWindow (新建WindowToken、WindowState，并将两个相关联，如果是APP则是创建AppWindowToken，不同的窗口对应不同的Token类型)  ->  在WindowState对象创建后会利用 win.attach()函数为当前APP申请建立SurfaceFlinger的链接（IWindowSession中一个SF的代理SurfaceSession） ->  在APP的performTraversals、relayoutWindow会时，调用mWindowSession.relayout向WMS申请或者更新Surface  ->  SurfaceComposerClient.createSurface（通过SF连创建填充Surface） -> WMS将Surface和SurfaceComposerClient封装成SurfaceControl返回给APP

      

    * 删除Window的过程

      WindowManagerGlobal.removeView  ->  WindowManagerGlobal.removeViewLocked  -> ViewRootImpl.die  ->  ViewRootImpl.doDie()  ->  ViewRootImpl.dispatchDetachedFromWindow  ->  destroySurface() ，mWindowSession.remove(mWindow)

      
      
      简单的总结为以下4点：
      
      1. 检查删除线程的正确性，如果不正确就抛出异常。
      
      2. 从ViewRootImpl列表、布局参数列表和View列表中删除与V对应的元素。
      
      3. 判断是否可以直接执行删除操作，如果不能就推迟删除操作。
      
      4. 执行删除操作，清理和释放与V相关的一切资源。
      
         
      
    * StartingWindow分析

      **启动流程**
      
      ![img](https://upload-images.jianshu.io/upload_images/2828107-49dd85bab26d5042.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)
      
      ![img](https://upload-images.jianshu.io/upload_images/2828107-026caae8f143fb4d.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)
      
      
      
      **移除流程**
      
      ![img](https://upload-images.jianshu.io/upload_images/2828107-9fc26e42f1ea27eb.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)
      
      

* 参考资料
  * [Android解析WindowManager - 刘望舒](http://liuwangshu.cn/framework/wm/3-add-window.html)
  
  * [Android解析WindowManagerService - 刘望舒](http://liuwangshu.cn/framework/wms/1-wms-produce.html)
  
  * [WMS启动流程分析](https://skytoby.github.io/2019/WMS%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B%E5%88%86%E6%9E%90/)
  
  * [android window(三)lWindow添加流程](https://www.cnblogs.com/mingfeng002/p/10948732.html)
  
  * [Android应用启动界面分析（Starting Window）](https://blog.csdn.net/xueshanhaizi/article/details/51262528)
  
  * [四大组件之Activity（二）-StartingWindow流程分析](https://www.jianshu.com/p/9740ab570df7)
  
    



### Android 显示系统

* 知识点
  
  ![img](https://upload-images.jianshu.io/upload_images/9696036-a39cc7b496fd8297.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)
  
  * 绘制渲染流程 
  
    vsync -> DisplayEventReceiver -> Choreographer.doFrame ->  Choreographer.doCallbacks -> ViewRootImpl.mTraversalRunnable.run -> ViewRootImpl.doTraversal() , removeSyncBarrier -> ViewRootImpl.performTraversals()
  
  * Surafe的创建流程；Surface、Canvas、Layer的关系；
  
    ViewRootImpl创建SurfaceControl（空） -> setContentView  -> WMS.addWindow()  ，同时创建WindowState与WindowStateAnimator，与SurfaceFlinger建立关系，创建真正的Surface（SurfaceControl）返回给ViewRootImpl。
  
    ![img](https://img2018.cnblogs.com/blog/821933/201907/821933-20190730200725525-636751839.png)
  
  * Surface的绘制
  
    View.onDraw()  ->  surface.lockCanvas()  ->  dequeueBuffer ->  surface.unlockCanvasAndPost()   ->  queueBuffer -> SurfaceFlinger和HW合成
  
    ![img](https://upload-images.jianshu.io/upload_images/9696036-770103d0b6d79ada.png)
  
* 参考资料
  
  * [Android UI 渲染机制的演进，你需要了解什么？](https://mp.weixin.qq.com/s/psrDADxwl782Fbs_vzxnQg)
  * [Android系统_图形系统总结](https://www.jianshu.com/p/238eb0a17760)
  * [Android图形系统篇总结](https://www.jianshu.com/p/180e1b6d0dcd)
  * [Android 显示系统：SurfaceFlinger详解](https://www.cnblogs.com/blogs-of-lxl/p/11272756.html)



### PackageManagerService源码分析







## 13. Android 三方开源库

### EventBus 事件总线

* 知识点

  <img src="https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/11/26/16ea6f0d941cb61c~tplv-t2oaga2asx-watermark.awebp" style="zoom: 67%;" />

  * 使用

    1. 定义事件

       ```java
       public class SayHelloEvent {
           private String message;
       
           public void sayHellow(String message) {
               this.message = message;
           }
       }
       ```

    2. 准备订阅者

       ```java
       
       @Subscribe(threadMode = ThreadMode.MAIN)
       public void onMessageEvent(SayHelloEvent event) {
           String message = event.getMessage();
           ...
       }
       
       @Override
       public void onStart() {
           super.onStart();
           EventBus.getDefault().register(this);
       }
        
       @Override
       public void onStop() {
           EventBus.getDefault().unregister(this);
           super.onStop();
       }
       ```

       创建时需要订阅者是调用`EventBus.getDefault().register(this)`方法进行注册，在适当的位置调用`EventBus.getDefault().unregister(this)`方法进行解注册。同时创建一个接收事件的方法`onMessageEvent`方法，使用注解`@Subscribe`进行标示，同时生命事件接收的线程为主线程。

    3. 发送事件

       ```java
       EventBus.getDefault().post(new SayHelloEvent("Hello,EventBus！！！"));
       ```

       在需要发送事件的地方调用`EventBus.getDefault().post`方法，将第一步创建的事件对象传入即可。

       

  * 5种线程模型

    线程模型是指事件订阅者所在线程和发布事件者所在线程的关系。

    ### POSTING

    事件的订阅和事件的发布处于同一线程。这是默认设置。事件传递意味着最少的开销，因为它完全避免了线程切换。因此，对于已知在非常短的时间内完成而不需要主线程的简单任务，这是推荐的模式。使用此模式的事件处理程序必须快速返回，以避免阻塞可能是主线程的发布线程。

    ### MAIN

    在主线程中执行响应方法。如果发布线程就是主线程，则直接调用订阅者的事件响应方法，否则通过主线程的 Handler 发送消息在主线程中处理——调用订阅者的事件响应函数。显然，`MainThread`类的方法也不能有耗时操作，以避免卡主线程。适用场景：**必须在主线程执行的操作**；

    ### MAIN_ORDERED

    在`Android`上，用户将在`Android`的主线程（`UI`线程）中被调用。与{`@link#MAIN`}不同，事件将始终排队等待传递。这确保了`post`调用是非阻塞的。

    ### BACKGROUND

    在后台线程中执行响应方法。如果发布线程**不是**主线程，则直接调用订阅者的事件响应函数，否则启动**唯一的**后台线程去处理。由于后台线程是唯一的，当事件超过一个的时候，它们会被放在队列中依次执行，因此该类响应方法虽然没有`PostThread`类和`MainThread`类方法对性能敏感，但最好不要有重度耗时的操作或太频繁的轻度耗时操作，以造成其他操作等待。适用场景：*操作轻微耗时且不会过于频繁*，即一般的耗时操作都可以放在这里；

    

    ### ASYNC
    
    不论发布线程是否为主线程，都使用一个空闲线程来处理。和`BackgroundThread`不同的是，`Async`类的所有线程是相互独立的，因此不会出现卡线程的问题。适用场景：*长耗时操作，例如网络访问*。


​    

  * 3种事件类型

    ### 普通事件

    普通事件是指已有的事件订阅者能够收到事件发送者发送的事件，在事件发送之后注册的事件接收者将无法收到事件。发送普通事件可以调用`EventBus.getDefault().post()`方法进行发送。

    ### 粘性事件

    粘性事件是指，不管是在事件发送之前注册的事件接收者还是在事件发送之后注册的事件接收者都能够收到事件。这里与普通事件的区别之处在于事件接收处需要定义事件接收类型，它可以通过`@Subscribe(threadMode = xxx, sticky = true)`的方式进行声明；在事件发送时需要调用`EventBus.getDefault().postSticky()`方法进行发送。事件类型默认为普通事件。

    ### 事件优先级

    订阅者优先级以影响事件传递顺序。在同一传递线程`ThreadMode`中，优先级较高的订阅者将在优先级较低的其他订阅者之前接收事件。默认优先级为`0`。注意：优先级不影响具有不同`ThreadMode`的订阅服务器之间的传递顺序！

    

  * 源码分析

    * EventBus.getDefault()创建EventBus对象

      > 通过单例模式创建EventBus对象

    * EventBus.getDefault().register(Object subscriber)的过程

      > 1、根据单例设计模式创建一个`EventBus`对象，同时创建一个`EventBus.Builder`对象对`EventBus`进行初始化，其中有三个比较重要的集合和一个`SubscriberMethodFinder`对象。
      >  2、调用`register`方法,首先通过反射获取到订阅者的`Class`对象。
      >  3、通过`SubscriberMethodFinder`对象获取订阅者中所有订阅的事件集合,它先从缓存中获取，如果缓存中有，直接返回；如果缓存中没有，通过反射的方式去遍历订阅者内部被注解的方法，将这些方法放入到集合中进行返回。
      >  4、遍历第三步获取的集合，将订阅者和事件进行绑定。
      >  5、在绑定之后会判断绑定的事件是否是粘性事件，如果是粘性事件，直接调用`postToSubscription`方法，将之前发送的粘性事件发送给订阅者。其实这也很好理解，在讲粘性事件时说过，如果在粘性事件发送之前注册的订阅者，当发送粘性事件时，会接收到该事件；如果是粘性事件发送之后注册的订阅者，同样也能接收到事件，原因就在这里。

      ![img](https://upload-images.jianshu.io/upload_images/1485091-8bf39ad48834f39c.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

    * EventBus.getDefault().unregister(Object subscriber)的过程

      > 1、获取订阅者的所有订阅方法，遍历这些方法。然后拿到每个方法对应的所有订阅者集合，将订阅者从集合中移除。
      > 2、移除订阅者中所有的订阅方法。

    * EventBus.getDefault().post(Object event)的过程

      > 1、获取当前线程的事件集合，将要发送的事件加入到集合中。
      >  2、通过循环，只要事件集合中还有事件，就一直发送。
      >  3、获取事件的`Class`对象，找到当前的`event`的所有父类和实现的接口的`class`集合。遍历这个集合，调用发送单个事件的方法进行发送。
      >  4、根据事件获取所有订阅它的订阅者集合，遍历集合，将事件发送给订阅者。
      >  5、发送给订阅者时，根据订阅方法的线程模式调用订阅方法，如果需要线程切换，则切换线程进行调用；否则，直接调用。

      ![img](https://upload-images.jianshu.io/upload_images/1485091-b7b63f83d65903d1.png?imageMogr2/auto-orient/strip|imageView2/2/w/988/format/webp)

    * EventBus.getDefault().postSticky(Object event)的过程

      > 1、将粘性事件加入到`EventBus`对象的粘性事件集合中，当有新的订阅者进入后，如果该订阅者订阅了该粘性事件，可以直接发送给订阅者。
      > 2、将粘性事件发送给已有的事件订阅者。

    * EventBus是如何进行线程间切换的？

      在`postToSubscription`时会根据ThreadMode来调用订阅者的方法，策略如上面的5种线程模型里面所写。切换到主线程会使用到mainThreadPoster，在构造函数中会初始化，其实就是一个主线程的Handler；切换到后台线程会使用到 backgroundPoster，是使用了线程池来执行。asyncPoster 和 backgroundPoster类似，也是使用了线程池来执行。

      ```java
          private void postToSubscription(Subscription subscription, Object event, boolean isMainThread) {
              switch (subscription.subscriberMethod.threadMode) {
                  case POSTING:
                      invokeSubscriber(subscription, event);
                      break;
                  case MAIN:
                      if (isMainThread) {
                          invokeSubscriber(subscription, event);
                      } else {
                          mainThreadPoster.enqueue(subscription, event);
                      }
                      break;
                  case MAIN_ORDERED:
                      if (mainThreadPoster != null) {
                          mainThreadPoster.enqueue(subscription, event);
                      } else {
                          invokeSubscriber(subscription, event);
                      }
                      break;
                  case BACKGROUND:
                      if (isMainThread) {
                          backgroundPoster.enqueue(subscription, event);
                      } else {
                          invokeSubscriber(subscription, event);
                      }
                      break;
                  case ASYNC:
                      asyncPoster.enqueue(subscription, event);
                      break;
                  default:
                      throw new IllegalStateException("Unknown thread mode: " + subscription.subscriberMethod.threadMode);
              }
          }
      
      ```

      

    * EventBus 索引

      EventBus3.0中增加了一个新特性：**通过在编译期创建索引（SubscriberInfoIndex）以提高程序运行性能**。在RunTime期间使用反射对程序运行的性能有较大影响

      

* 参考资料
  * [EventBus源码解析](https://juejin.cn/post/6844904007199113229)
  * [EventBus 3.0 源码分析](https://www.jianshu.com/p/f057c460c77e)
  * [EventBus3.0 性能提升之添加索引](https://blog.csdn.net/kaifa1321/article/details/79761507)



### RxJava 

* 知识点
  * 简介

    * Observable/Observer/ObserableEmitter

      ![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/afb793854f364a34a5321de53daa1361~tplv-k3u1fbpfcp-watermark.awebp)

    * Flowable/Subscriber/FlowableEmitter

      ```java
      Flowable.create(new FlowableOnSubscribe<Integer>() {
                  @Override
                  public void subscribe(FlowableEmitter<Integer> emitter) throws Exception {
                      Log.d(TAG, "emit 1");
                      emitter.onNext(1);
                      Log.d(TAG, "emit 2");
                      emitter.onNext(2);
                      Log.d(TAG, "emit 3");
                      emitter.onNext(3);
                      Log.d(TAG, "emit complete");
                      emitter.onComplete();
                  }
              }, BackpressureStrategy.ERROR).subscribeOn(Schedulers.io())
                      .observeOn(AndroidSchedulers.mainThread())
                      .subscribe(new Subscriber<Integer>() {
      
                          @Override
                          public void onSubscribe(Subscription s) {
                              Log.d(TAG, "onSubscribe");
                              mSubscription = s;  
                              s.request(1);
                          }
      
                          @Override
                          public void onNext(Integer integer) {
                              Log.d(TAG, "onNext: " + integer);
                              mSubscription.request(1);
                          }
      
                          @Override
                          public void onError(Throwable t) {
                              Log.w(TAG, "onError: ", t);
                          }
      
                          @Override
                          public void onComplete() {
                              Log.d(TAG, "onComplete");
                          }
                      });
      ```

      

    * Disposable / CompositeDisposable - 断连

    * 其他观察者

      在 RxJava2.x 中还有三种类型的Observables：Single、Completable、Maybe。

      | 类型          | 描述                                                         |
      | ------------- | ------------------------------------------------------------ |
      | Observable<T> | 能够发射0或n个数据，并以成功或错误事件终止。                 |
      | Flowable<T>   | 能够发射0或n个数据，并以成功或错误事件终止。 支持Backpressure，可以控制数据源发射的速度。 |
      | Single<T>     | 只发射单个数据或错误事件。                                   |
      | Completable   | 它从来不发射数据，只处理 onComplete 和 onError 事件。可以看成是Rx的Runnable。 |
      | Maybe<T>      | 能够发射0或者1个数据，要么成功，要么失败。有点类似于Optional |

      

  * 常用操作符

    * 分类

      * 创建操作符：https://www.jianshu.com/p/e19f8ed863b1

      * 变换操作符：https://www.jianshu.com/p/904c14d253ba

      * 组合/合并操作符：https://www.jianshu.com/p/c2a7c03da16d

      * 功能操作符：https://www.jianshu.com/p/b0c3669affdb

      * 过滤操作符：https://www.jianshu.com/p/c3a930a03855

      * 条件/布尔操作符：https://www.jianshu.com/p/954426f90325

        

  * 线程调度

    * subscribeOn / observeOn

    * 多次指定上游的线程只有第一次指定的有效, 也就是说多次调用`subscribeOn()` 只有第一次的有效, 其余的会被忽略.

      多次指定下游的线程是可以的, 也就是说每调用一次`observeOn()` , 下游的线程就会切换一次.

    * 线程类型

      | 类型                           | 含义                  | 应用场景                         |
      | ------------------------------ | --------------------- | -------------------------------- |
      | Schedulers.trampoline()        | 当前线程 = 不指定线程 | 默认                             |
      | AndroidSchedulers.mainThread() | Android主线程         | 操作UI                           |
      | Schedulers.newThread()         | 常规新线程            | 耗时等操作                       |
      | Schedulers.io()                | io操作线程            | 网络请求、读写文件等io密集型操作 |
      | Schedulers.computation()       | CPU计算操作线程       | 大量计算操作                     |

      

  * 异常处理

  * Flowable背压

    一种 控制事件流速 的策略。在 **异步订阅关系** 中，控制事件发送 & 接收的速度，解决了 因被观察者发送事件速度 与 观察者接收事件速度 不匹配（一般是前者 快于 后者），从而导致观察者无法及时响应 / 处理所有 被观察者发送事件 的问题。

    

    **背压策略**

    ![img](https://imgconvert.csdnimg.cn/aHR0cDovL3VwbG9hZC1pbWFnZXMuamlhbnNodS5pby91cGxvYWRfaW1hZ2VzLzk0NDM2NS00N2I1NWVkZWMyOTlmYWVhLnBuZz9pbWFnZU1vZ3IyL2F1dG8tb3JpZW50L3N0cmlwJTdDaW1hZ2VWaWV3Mi8yL3cvMTI0MA)

    **模式1：BackpressureStrategy.ERROR**

    - 问题：发送事件速度 ＞ 接收事件 速度，即流速不匹配

      > 具体表现：出现当缓存区大小存满（默认缓存区大小 = 128）、被观察者仍然继续发送下1个事件时

    - 处理方式：直接抛出异常`MissingBackpressureException`

    

    **模式2：BackpressureStrategy.MISSING**

    - 问题：发送事件速度 ＞ 接收事件 速度，即流速不匹配

      > 具体表现是：出现当缓存区大小存满（默认缓存区大小 = 128）、被观察者仍然继续发送下1个事件时

    - 处理方式：友好提示：缓存区满了，会调用OnError（）

    

    **模式3：BackpressureStrategy.BUFFER**

    * 问题：发送事件速度 ＞ 接收事件 速度，即流速不匹配

      > 具体表现是：出现当缓存区大小存满（默认缓存区大小 = 128）、被观察者仍然继续发送下1个事件时

    * 处理方式：将缓存区大小设置成无限大

      > 即 被观察者可无限发送事件 观察者，但实际上是存放在缓存区
    > 但要注意内存情况，防止出现OOM

    

    **模式4： BackpressureStrategy.DROP**

    * 问题：发送事件速度 ＞ 接收事件 速度，即流速不匹配

      > 具体表现是：出现当缓存区大小存满（默认缓存区大小 = 128）、被观察者仍然继续发送下1个事件时

    * 处理方式：超过缓存区大小（128）的事件丢弃

      > 如发送了150个事件，仅保存第1 - 第128个事件，第129 -第150事件将被丢弃

      

    **模式5：BackpressureStrategy.LATEST**

    * 问题：发送事件速度 ＞ 接收事件 速度，即流速不匹配

      > 具体表现是：出现当缓存区大小存满（默认缓存区大小 = 128）、被观察者仍然继续发送下1个事件时

    * 处理方式：只保存最新（最后）事件，超过缓存区大小（128）的事件丢弃

      > 即如果发送了150个事件，缓存区里会保存129个事件（第1-第128 + 第150事件）

    

  * 源码分析 - 查看次文章 [详解 RxJava 的消息订阅和线程切换原理](https://juejin.cn/post/6844903619947397134#heading-0)

    * 消息订阅

      1. 总结一下流程：

         1）Obserable 调用create方法，ObservableOnSubscribe作为上游参数，会创建出一个ObservableCreate类。而此处需要注意 Observable是一个抽象类，RxJava中的操作符都会对应一个`Observable`的子类，例如`ObservableCreate`、`ObservableFromCallable`等。

         2）创建出对应的Obserable 后（此处为`ObservableCreate`），调用子类的subscribe()方法，会新创建出一个CreateEmitter发射器，此发射器实现了`Disposable`接口，所以也是一个`Disposable`对象，此发射器会同时传递给上游和下游，所以发射器其实是一个连接器。通过observer.onSubscribe(parent)把Emitter给Observer，添加监听者；通过source.subscribe(parent) 把将Emitter与ObservableOnSubscribe添加订阅。

         3）在上游的subscribe的方法中通过调用emmiter的onNext或onComplete方法，然后emmiter会在内部传递给下游Observer来执行对应的方法。

      ![Observable.create()时序图.png](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/6/12/163f3fc55ef0d5ef~tplv-t2oaga2asx-watermark.awebp)

      ![订阅过程时序图.png](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/6/12/163f3fc55f4f9e71~tplv-t2oaga2asx-watermark.awebp)

      

    * 切断消息

      1）Disposable是一个接口，可以理解Disposable为一个连接器，调用dispose()后，这个连接器将会中断。其具体实现在`CreateEmitter`类，所以看` CreateEmitter.dispose()` 即可。

      2）在` CreateEmitter.dispose()` 只是调用了`DisposableHelper.dispose(this)` , DisposableHelper中有一个标识位来标识是否中断，通过isDisposed()来判断。

      3）`CreateEmitter`类中的方法会通过isDisposed来进行判断是否发送，如果中断了就不会发送。所以onError 和 onComplete 是互斥的，因为他们发送之后会进行中断。

      

    * 线程切换

      > `Observer`（观察者）的`onSubscribe()`方法运行在当前线程中。
      >
      > `Observable`（被观察者）中的`subscribe()`运行在`subscribeOn()`指定的线程中。
      >
      > `Observer`（观察者）的`onNext()`和`onComplete()`等方法运行在`observeOn()`指定的线程中。
      >
      > 
      >
      > 多次调用`subscribeOn()`，不断的进行封装，会从外向内不断调用，最终使用了第一次设置的线程。多次调用`observeOn` 则就以最后一次的为准。

      1） subscribeOn()

      ​      subscribeOn实际是创建了ObservableSubscribeOn的Observable，它的订阅方法里面创建了`SubscribeOnObserver`，通过线程池执行Runnable来达到上游Observable的订阅在子线程中执行

      ​	![subscribeOn()切换线程时序图.png](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/6/12/163f3fc597417a8c~tplv-t2oaga2asx-watermark.awebp)

      

      2）observeOn()

      ​     observeOn实际是创建了ObservableObserveOn的Observable，它的订阅方法里面创建了`ObserveOnObserver`，而`ObserveOnObserver`是实现了Runnable接口，把它包装成message给主线程的Handler发送一条消息，而`ObserveOnObserver`的run方法中会给下游的Observer发送数据。

      

      ![ObservableObserveOn.png](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/6/12/163f3fc59a40251e~tplv-t2oaga2asx-watermark.awebp)

      

      ![observeOn()时序图.png](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/6/12/163f3fc5bcad8321~tplv-t2oaga2asx-watermark.awebp)

* 参考资料
  * [使用 - RxJava2 只看这一篇文章就够了](https://juejin.cn/post/6844903617124630535#heading-0)
  * [使用 - 给初学者的RxJava2.0教程](https://www.jianshu.com/c/299d0a51fdd4)
  * [RxJava 详细教程](https://blog.csdn.net/carson_ho/category_7227390.html)
  * [详解 RxJava 的消息订阅和线程切换原理](https://juejin.cn/post/6844903619947397134#heading-0)
  * [源码 - 史上最简单的 RxJava 源码分析](https://zhuanlan.zhihu.com/p/129889972)
  * [源码 - RxJava面经一，拿去，不谢!](https://juejin.cn/post/6900870262062120967) 



### <span id="okhttp">okhttp</span>

* 使用

  ```java
  OkHttpClient okHttpClient = new OkHttpClient.Builder()
          .build();
  Request request = new Request.Builder()
          .url(url)
          .build();
  okHttpClient.newCall(request).enqueue(new Callback() {
      @Override
      public void onFailure(Call call, IOException e) {
      }
  
      @Override
      public void onResponse(Call call, Response response) throws IOException {
  
      }
  });
  
  ```

  

* 知识点

  ![类图](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e7d45719cb384035a69385400f71b338~tplv-k3u1fbpfcp-watermark.awebp)

  

  ![img](https://upload-images.jianshu.io/upload_images/2383280-9b72edff43b2db5e.png)

  

  

  * 拦截器（责任链模式）

    ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/1/30/16146b3c0a2ee472~tplv-t2oaga2asx-watermark.awebp)

  * Http缓存

    1. 基于HTTP的缓存机制实现
    2. 缓存了Response
    3. 使用DiskLruCache

  * Socket连接池复用

    * RealConnection：真正建立连接的对象，利用Socket建立连接。
    * ConnectionPool：连接池，用来管理和复用连接。连接池最多保 持5个地址的连接keep-alive，每个keep-alive时长为5分钟，并有异步线程清理无效的连接

* 参考资料
  * [Networking with the OkHttp Library - CodePath - 使用篇](https://github.com/codepath/android_guides/wiki/Using-OkHttp)
  * [Android开源框架源码鉴赏：Okhttp](https://juejin.cn/post/6844903557632622605)
  * [OkHttp 4源码（7）— 总结](https://www.jianshu.com/p/c988d0416020)
  * [OkHttp源码阅读指南](https://juejin.cn/post/6951936883760988191)



### <span id="Retrofit">Retrofit</span>

* 知识点

  * 使用介绍

    步骤1：添加Retrofit库的依赖
    步骤2：创建 接收服务器返回数据 的类
    步骤3：创建 用于描述网络请求 的接口
    步骤4：创建 Retrofit 实例
    步骤5：创建 网络请求接口实例 并 配置网络请求参数
    步骤6：发送网络请求（异步 / 同步）

    > 封装了 数据转换、线程切换的操作

    步骤7：处理服务器返回的数据
  
* 参考资料
  * [深入浅出 Retrofit，这么牛逼的框架你们还不来看看？](https://mp.weixin.qq.com/s?__biz=MzA3NTYzODYzMg==&mid=2653577186&idx=1&sn=1a5f6369faeb22b4b68ea39f25020d28&scene=1&srcid=06039K4A2eGkHPxLbKED09Mk)
  * [Consuming APIs with Retrofit -  CodePath - 使用篇](https://github.com/codepath/android_guides/wiki/Consuming-APIs-with-Retrofit)



### Glide

* 知识点
  * 使用
    * 简单使用 ：Glide.with(this).load(imgUrl).into(mIv1);
    
    * 参数配置
    
      ```java
      RequestOptions options = new RequestOptions()
                  .placeholder(R.drawable.ic_launcher_background)
                  .error(R.mipmap.ic_launcher)
                  .diskCacheStrategy(DiskCacheStrategy.NONE)
          		.override(200, 100);
          
      Glide.with(this)
                  .load(imgUrl)
                  .apply(options)
                  .into(mIv2);
      
      
      ```
    
      
    
  * **为什么使用？**

    * 图片 + 媒体 缓存。  适用于更多的内容表现形式（如Gif、WebP、缩略图、Video）
    * 生命周期集成 （根据Activity或者Fragment的生命周期管理图片加载请求）
    * 高效处理Bitmap  （bitmap的复用和主动回收，减少系统回收压力）
    * 高效的缓存策略，灵活（Picasso只会缓存原始尺寸的图片，Glide缓存的是多种规格），加载速度快且内存开销小，默认使用RGB565（默认Bitmap格式的不同，使得内存开销是Picasso（默认RGB888）的一半。**Glide4.0之后默认也是RGB8888了。**

    

  * **执行流程**

    整个图片加载过程，就是with得到`RequestManager`，load得到`RequestBuilder`，然后into开启加载：

    创建`Request`、开启`Engine`、运行`DecodeJob`线程、`HttpUrlFetcher`加载网络数据、回调给载体`Target`、载体为`ImageView`设置展示图片。

    

    ![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/7/15/17351bf4013357d3~tplv-t2oaga2asx-watermark.awebp)

    

  * **生命周期控制**

    1.调用时通过`Glide.with`传入`context`,利用`context`构建一个`Fragment`
    2.监听`Fragment`生命周期，销毁时释放`Glide`资源

    

    `Glide.with(this)`绑定了`Activity`的生命周期。在`Activity`内新建了一个无`UI`的`Fragment`，这个`Fragment`持有一个`Lifecycle`，通过`Lifecycle`在`Fragment`关键生命周期通知`RequestManager进`行相关从操作。在生命周期`onStart`时继续加载，`onStop`时暂停加载，`onDestory`时停止加载任务和清除操作。

    

  * **Glide的内存优化策略**

    1. 尺寸优化

    2. 图片格式优化

       在`API29`中，将`Bitmap`分为`ALPHA_8`, `RGB_565`, `ARGB_4444`, `ARGB_8888`, `RGBA_F16`, `HARDWARE`六个等级。

       - `ALPHA_8`：不存储颜色信息，每个像素占1个字节；
       - `RGB_565`：仅存储`RGB`通道，每个像素占2个字节，对`Bitmap`色彩没有高要求，可以使用该模式；
       - `ARGB_4444`：已弃用，用`ARGB_8888`代替；
       - `ARGB_8888`：每个像素占用4个字节，保持高质量的色彩保真度，默认使用该模式；
       - `RGBA_F16`：每个像素占用8个字节，适合宽色域和`HDR`；
       - `HARDWARE`：一种特殊的配置，减少了内存占用同时也加快了`Bitmap`的绘制。

       每个等级每个像素所占用的字节也都不一样，所存储的色彩信息也不同。同一张100像素的图片，`ARGB_8888`就占了400字节，`RGB_565`才占200字节，RGB_565在内存上取得了优势，但是`Bitmap`的色彩值以及清晰度却不如`ARGB_8888`模式下的`Bitmap`

       值得注意的是在`Glide4.0`之前,`Glide`默认使用`RGB565`格式，比较省内存
        但是`Glide4.0`之后，默认格式已经变成了`ARGB_8888`格式了,这一优势也就不存在了。
        这本身也就是质量与内存之间的取舍，如果应用所需图片的质量要求不高，也可以修改默认格式

       

    3. 内存复用优化

       inBitmap 和 BitmapPool

       

  * **缓存机制（四级缓存）**

    | 缓存类型          | 缓存代表            | 说明                                                         |
    | ----------------- | ------------------- | ------------------------------------------------------------ |
    | 活动缓存          | ActiveResources     | 如果当前对应的图片资源是从内存缓存中获取的，那么会将这个图片存储到活动资源中。 |
    | 内存缓存          | LruResourceCache    | 图片解析完成并最近被加载过，则放入内存中                     |
    | 磁盘缓存-资源类型 | DiskLruCacheWrapper | 被解码后的图片写入磁盘文件中                                 |
    | 磁盘缓存-原始数据 | DiskLruCacheWrapper | 网络请求成功后将原始数据在磁盘中缓存                         |

    在介绍具体缓存前，先来看一张加载缓存执行顺序：
     ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/750d9bb09f2e4e8c8b0975ec43b941fa~tplv-k3u1fbpfcp-watermark.awebp)

    

    **内存缓存**流程（ActiveResources、LruResourceCache）：

    ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ec340f2b185247e9b72c582bfa252aa2~tplv-k3u1fbpfcp-watermark.awebp)

    

    **硬盘缓存**：主要是`DiskCacheStrategy.RESOURCE`缓存的是变换后的资源，`DiskCacheStrategy.DATA`缓存的是变换前的资源

    

    `DiskCacheStrategy.NONE`： 表示不缓存任何内容。

    `DiskCacheStrategy.RESOURCE`： 在资源解码后将数据写入磁盘缓存，即经过缩放等转换后的图片资源。

    `DiskCacheStrategy.DATA`： 在资源解码前将原始数据写入磁盘缓存。

    `DiskCacheStrategy.ALL` ： 使用`DATA`和`RESOURCE`缓存远程数据，仅使用`RESOURCE`来缓存本地数据。

    `DiskCacheStrategy.AUTOMATIC`：它会尝试对本地和远程图片使用最佳的策略。当你加载远程数据时，`AUTOMATIC` 策略仅会存储未被你的加载过程修改过的原始数据，因为下载远程数据相比调整磁盘上已经存在的数据要昂贵得多。对于本地数据，`AUTOMATIC` 策略则会仅存储变换过的缩略图，因为即使你需要再次生成另一个尺寸或类型的图片，取回原始数据也很容易。默认使用这种缓存策略

    

* 参考资料
  * [Android主流三方库源码分析（三、深入理解Glide源码）](https://jsonchao.github.io/2018/12/16/Android%E4%B8%BB%E6%B5%81%E4%B8%89%E6%96%B9%E5%BA%93%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90%EF%BC%88%E4%B8%89%E3%80%81%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3Glide%E6%BA%90%E7%A0%81%EF%BC%89/)
  * [【带着问题学】Glide做了哪些优化?](https://juejin.cn/post/6970683481127043085)
  * [面试官：简历上最好不要写Glide，不是问源码那么简单](https://juejin.cn/post/6844903986412126216)
  * [Android 【手撕Glide】--Glide缓存机制](https://www.jianshu.com/p/b85f89fce019)



### LeakCanary

* 知识点

  1. LeakCanary检测内存泄漏的原理与基本流程

     * 1.1 内存泄露原理

       引用链来自于垃圾回收器的可达性分析算法：当一个对象到`GC Roots` 没有任何引用链相连时，则证明此对象是不可用的。如图：
        ![img](https://raw.githubusercontent.com/shenzhen2017/newImage/master/blog11/p3.jpg)
        对象`object5`、`object6`、`object7` 虽然互相有关联，但是它们到 `GC Roots` 是不可达的，所以它们将会被判定为是可回收的对象。
        在`Java`语言中，可作为`GC Roots`的对象包括下面几种：

       - 虚拟机栈（栈帧中的本地变量表）中引用的对象。
       - 方法区中静态属性引用的对象。
       - 方法区中常量引用的对象。
       - 本地方法栈中JNI（即一般说的Native方法）引用的对象。

     * 1.2 LeakCanary检测内存泄漏的基本流程

       知道了内存泄漏的原理，我们可以推测到`LeakCanary`的基本流程大概是怎样的
        1.在页面关闭后触发检测(不再需要的对象)
        2.触发`GC`，然后获取仍然存在的对象，这些是可能泄漏的
        3.`dump heap`然后分析`hprof`文件，构建可能泄漏的对象与`GCRoot`间的引用链,如果存在则证明泄漏
        4.存储结果并使用通知提醒用户存在泄漏

       总体流程图如下所示：
        ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1673c300751d483c982d421907b007e0~tplv-k3u1fbpfcp-watermark.awebp)

       - 1.`ObjectWatcher` 创建了一个`KeyedWeakReference`来监视对象.
       - 2.稍后，在后台线程中，延时检查引用是否已被清除，如果没有则触发`GC`
       - 3.如果引用一直没有被清除，它会`dumps the heap` 到一个`.hprof` 文件中，然后将`.hprof` 文件存储到文件系统。
       - 4.分析过程主要在`HeapAnalyzerService`中进行，`Leakcanary2.0`中使用`Shark`来解析`hprof`文件。
       - 5.`HeapAnalyzer` 获取`hprof`中的所有`KeyedWeakReference`，并获取`objectId`
       - 6.`HeapAnalyzer`计算`objectId`到`GC Root`的最短强引用链路径来确定是否有泄漏，然后构建导致泄漏的引用链。
       - 7.将分析结果存储在数据库中，并显示泄漏通知。


​       

  2. LeakCanary是如何自动安装的？

     `LeakCanary`的使用非常方便，只需要添加依赖便可以自动初始化，这是如何实现的呢？
      我们看一下源码，其实主要是通过`ContentProvider`实现的

     ```kotlin
     internal sealed class AppWatcherInstaller : ContentProvider() {
     
       /**
        * [MainProcess] automatically sets up the LeakCanary code that runs in the main app process.
        */
       internal class MainProcess : AppWatcherInstaller()
     
       /**
        * When using the `leakcanary-android-process` artifact instead of `leakcanary-android`,
        * [LeakCanaryProcess] automatically sets up the LeakCanary code
        */
       internal class LeakCanaryProcess : AppWatcherInstaller()
     
       override fun onCreate(): Boolean {
         val application = context!!.applicationContext as Application
         AppWatcher.manualInstall(application)
         return true
       }
     }  
     ```

     当我们启动`App`时，一般启动顺序为：`Application`->`attachBaseContext` =====>`ContentProvider`->`onCreate` =====>`Application`->`onCreate`
      `ContentProvider`会在`Application.onCreate`前初始化，这样就调用到了`LeakCanary`的初始化方法实现了免手动初始化。
     
     
     
* 跨进程初始化
  
  注意,`AppWatcherInstaller`有两个子类,`MainProcess`与`LeakCanaryProcess`
        其中默认使用`MainProcess`,会在`App`进程初始化
        有时我们考虑到`LeakCanary`比较耗内存，需要在独立进程初始化
        使用`leakcanary-android-process`模块的时候，会在一个新的进程中去开启`LeakCanary`
  
* LeakCanary2.0手动初始化的方法

  `LeakCanary`在检测内存泄漏时比较耗时，同时会打断`App`操作，在不需要检测时的体验并不太好，所以虽然`LeakCanary`可以自动初始化，但我们有时其实还是需要手动初始化

  `LeakCanary`的自动初始化可以手动关闭

  ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <resources>
             <bool name="leak_canary_watcher_auto_install">false</bool>
        </resources>
  ```

  1. 然后在需要初始化的时候，调用`AppWatcher.manualInstall`即可
  2. 是否开始`dump`与分析开头：`LeakCanary.config = LeakCanary.config.copy(dumpHeap = false)`
  3. 桌面图标开头：重写`R.bool.leak_canary_add_launcher_icon`或者调用`LeakCanary.showLeakDisplayActivityLauncherIcon(false)`

  

* LeakCanary如何检测内存泄漏?

  * 3.1 初始化的时候做了什么？（AppWatcher.manualInstall）

    ```kotlin
      @JvmOverloads
      fun manualInstall(
        application: Application,
        retainedDelayMillis: Long = TimeUnit.SECONDS.toMillis(5),
        watchersToInstall: List<InstallableWatcher> = appDefaultWatchers(application)
      ) {
        ....
        watchersToInstall.forEach {
          it.install()
        }
      }
    
      fun appDefaultWatchers(
        application: Application,
        reachabilityWatcher: ReachabilityWatcher = objectWatcher
      ): List<InstallableWatcher> {
        return listOf(
          ActivityWatcher(application, reachabilityWatcher),
          FragmentAndViewModelWatcher(application, reachabilityWatcher),
          RootViewWatcher(reachabilityWatcher),
          ServiceWatcher(reachabilityWatcher)
        )
      }
    
    ```

    可以看出，初始化时即安装了一些`Watcher`，即在默认情况下，我们只会观察`Activity`,`Fragment`,`RootView`,`Service`这些对象是否泄漏
    如果需要观察其他对象，需要手动添加并处理

  * 3.2 LeakCanary如何触发检测?

    如上文所述，在初始化时会安装一些`Watcher`，我们以`ActivityWatcher`为例

    ```kotlin
    class ActivityWatcher(
      private val application: Application,
      private val reachabilityWatcher: ReachabilityWatcher
    ) : InstallableWatcher {
    
      private val lifecycleCallbacks =
        object : Application.ActivityLifecycleCallbacks by noOpDelegate() {
          override fun onActivityDestroyed(activity: Activity) {
            reachabilityWatcher.expectWeaklyReachable(
              activity, "${activity::class.java.name} received Activity#onDestroy() callback"
            )
          }
        }
    
      override fun install() {
        application.registerActivityLifecycleCallbacks(lifecycleCallbacks)
      }
    
      override fun uninstall() {
        application.unregisterActivityLifecycleCallbacks(lifecycleCallbacks)
      }
    }
    ```

    可以看到在`Activity.onDestory`时，就会触发检测内存泄漏

    

  * 3.3 LeakCanary如何检测可能泄漏的对象?

    从上面可以看出，`Activity`关闭后会调用到`ObjectWatcher.expectWeaklyReachable`

    ```kotlin
    @Synchronized override fun expectWeaklyReachable(
        watchedObject: Any,
        description: String
      ) {
        if (!isEnabled()) {
          return
        }
        removeWeaklyReachableObjects()
        val key = UUID.randomUUID()
          .toString()
        val watchUptimeMillis = clock.uptimeMillis()
        val reference =
          KeyedWeakReference(watchedObject, key, description, watchUptimeMillis, queue)
        SharkLog.d {
          "Watching " +
            (if (watchedObject is Class<*>) watchedObject.toString() else "instance of ${watchedObject.javaClass.name}") +
            (if (description.isNotEmpty()) " ($description)" else "") +
            " with key $key"
        }
    
        watchedObjects[key] = reference
        checkRetainedExecutor.execute {
          moveToRetained(key)
        }
      }
    
    private fun removeWeaklyReachableObjects() {
        // WeakReferences are enqueued as soon as the object to which they point to becomes weakly
        // reachable. This is before finalization or garbage collection has actually happened.
        var ref: KeyedWeakReference?
        do {
          ref = queue.poll() as KeyedWeakReference?
          if (ref != null) {
            watchedObjects.remove(ref.key)
          }
        } while (ref != null)
      }  
    ```

    可以看出

    1. 传入的观察对象都会被存储在`watchedObjects`中
    2. 会为每个`watchedObject`生成一个`KeyedWeakReference`弱引用对象并与一个`queue`关联，当对象被回收时，该弱引用对象将进入`queue`当中
    3. 在检测过程中，我们会调用多次`removeWeaklyReachableObjects`,将已回收对象从`watchedObjects`中移除
    4. 如果`watchedObjects`中没有移除对象，证明它没有被回收，那么就会调用`moveToRetained`。

    

  * 3.4 LeakCanary触发堆快照，生成hprof文件

    `moveToRetained`之后会调用到`HeapDumpTrigger.checkRetainedInstances`方法
     `checkRetainedInstances()` 方法是确定泄露的最后一个方法了。
     这里会确认引用是否真的泄露，如果真的泄露，则发起 `heap dump`，分析 `dump` 文件，找到引用链

    ```kotlin
    private fun checkRetainedObjects() {
        var retainedReferenceCount = objectWatcher.retainedObjectCount
    
        if (retainedReferenceCount > 0) {
          gcTrigger.runGc()
          retainedReferenceCount = objectWatcher.retainedObjectCount
        }
    
        if (checkRetainedCount(retainedReferenceCount, config.retainedVisibleThreshold)) return
    
        val now = SystemClock.uptimeMillis()
        val elapsedSinceLastDumpMillis = now - lastHeapDumpUptimeMillis
        if (elapsedSinceLastDumpMillis < WAIT_BETWEEN_HEAP_DUMPS_MILLIS) {
          onRetainInstanceListener.onEvent(DumpHappenedRecently)
          ....
          return
        }
    
        dismissRetainedCountNotification()
        val visibility = if (applicationVisible) "visible" else "not visible"
        dumpHeap(
          retainedReferenceCount = retainedReferenceCount,
          retry = true,
          reason = "$retainedReferenceCount retained objects, app is $visibility"
        ) 
    }
    
      private fun dumpHeap(
        retainedReferenceCount: Int,
        retry: Boolean,
        reason: String
      ) {
         ....
      	 heapDumper.dumpHeap()
      	 ....
         lastDisplayedRetainedObjectCount = 0
         lastHeapDumpUptimeMillis = SystemClock.uptimeMillis()
         objectWatcher.clearObjectsWatchedBefore(heapDumpUptimeMillis)
         HeapAnalyzerService.runAnalysis(
           context = application,
           heapDumpFile = heapDumpResult.file,
           heapDumpDurationMillis = heapDumpResult.durationMillis,
           heapDumpReason = reason
         )
     }
    }
    ```
    

  1.如果`retainedObjectCount`数量大于0，则进行一次`GC`,避免额外的`Dump`
     2.默认情况下，如果`retainedReferenceCount<5`，不会进行`Dump`，节省资源
     3.如果两次`Dump`之间时间少于60s，也会直接返回，避免频繁`Dump`
     4.调用`heapDumper.dumpHeap()`进行真正的`Dump`操作
     5.`Dump`之后，要删除已经处理过了的引用
     6.调用`HeapAnalyzerService.runAnalysis`对结果进行分析

  

  * 3.5 LeakCanary如何分析hprof文件

    分析`hprof`文件的工作主要是在`HeapAnalyzerService`类中完成的.

    解析流程如下所示:
     ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a9c48844833244c4957fe5a6eb04508c~tplv-k3u1fbpfcp-watermark.awebp)
     简要说下流程：
     1.解析文件头信息，得到解析开始位置
     2.根据头信息创建`Hprof`文件对象
     3.构建内存索引
     4.使用`hprof`对象和索引构建`Graph`对象
     5.查找可能泄漏的对象与`GCRoot`间的引用链来判断是否存在泄漏(使用广度优先算法在`Graph`中查找)
    
    
    
  * 3.6 泄漏结果存储与通知

    结果的存储与通知主要在DefaultOnHeapAnalyzedListener中完成，主要做了两件事：

    1.存储泄漏分析结果到数据库中
    2.展示通知，提醒用户去查看内存泄漏情况

  

  4. 为什么LeakCanary不能用于线上?

     * 每次内存泄漏以后，都会生成一个`.hprof`文件，然后解析，并将结果写入`.hprof.result`。增加手机负担，引起手机卡顿等问题。
     * 多次调用`GC`，可能会对线上性能产生影响
     * 同样的泄漏问题，会重复生成 `.hprof` 文件，重复分析并写入磁盘。
     * `.hprof`文件较大，信息回捞成问题。

* 参考资料
  
  * [【带着问题学】关于LeakCanary2.0你应该知道的知识点](https://juejin.cn/post/6968084138125590541)
  * [看完这篇 LeakCanary 原理分析，又可以虐面试官了！](https://mp.weixin.qq.com/s/1jFY_22hoWgCw3CDo2rpOA)





# 面经

