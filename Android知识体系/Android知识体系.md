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

* UID、UserID、AppId、Pid

  * android中uid用于标识一个应用程序，uid在应用安装时被分配，并且在应用存在于手机上期间，都不会改变，范围是从10000开始，到19999结束,而且，UID由用户ID(UserId)和应用ID(AppId)共同决定
  * 系统中会有多个用户 (User，即手机里的主机、访客等多用户), 每个用户也有一个唯一的 ID 值, 称为"UserId"
  * Pid就是各进程的身份标识,程序一运行系统就会自动分配给进程一个独一无二的PID
  * AppID跟app相关，包名相同的appid都一样，即使是不同用户

* 进程的内存分配

  * 内存类型

    Android 设备包含三种不同类型的内存：RAM、zRAM 和存储器。

  * 内存页

    Android 的物理内存被分为多个「页」（page）。通常，每个页拥有 4KB 的内存。有三种页类型：已用页、缓存页、空闲页。

  * 统计内存占用

    * 常驻内存大小 (Resident Set Size - **RSS**) ： 应用使用的 **共享页 + 非共享页的数量**

    * 按比例分摊的内存大小 (Proportional Set Size - **PSS**) ： 应用使用的 **非共享页数量 + 共享页均匀分摊数量**（例如，如果三个进程共享 3MB，则每个进程的 PSS 为 1MB）

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
  * 杀死之后再次拉起
  
  * 进程启动/APP启动
  
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

  * 进程间通信方式
    
    * 使用Bundle，1M大小限制
    * 使用文件共享
    * 使用Socket
    * 使用Binder（使用AIDL、使用Messenger、使用ContentProvider）
    
  * IPC通信知识
    
    * Binder（问题：）
    
    1. Binder可以理解为一种虚拟的物理设备，他的设备驱动是 /dev/binder
      2. Binder调用时耗时的，客户端发起远程请求时，当前线程会被挂起直至服务器进程返回数据；服务端的Binder方法运行在Binder的线程中；因此Binder调用不能在主线程中
      3. Binder有两个重要的方法：linkToDeath 和 unLinkToDeath ，给BInder设置死亡代理。声明一个IBinder.DeathRecipient对象，再调用binder.linkToDeath(mDeathRecipient，0)。
    4. Binder连接池。设计逻辑是把所有的AIDL放在同一个Service管理。服务端提供一个queryBinder接口，这个接口能够根据业务模块的特征来返回相应的Binder对象给他们。详情可以参考《Android开发艺术探索》第2章2.5Binder连接池  章节。
    
  * AIDL（问题：如何实现、参数 in、out、inout、参数oneway）
    
      1. 创建 .aidl 文件，一个接口IInterface ，一个抽象类 IBinder
      2. 内部类Stub 和 代理类Proxy，Stub为服务端，Proxy为客户端
      3. 几个重要的方法：DESCRIPTOR，asInterface，asBinder，transact，onTransact
      4. .aidl文件中的参数in代表只能由客户端流向服务端；out 表示数据只能由服务端流向客户端；inout 则表示数据可在服务端与客户端之间双向流通；**oneway关键字用于修改远程调用的行为，**被oneway修饰了的方法不可以有返回值，也不可以有带out或inout的参数
      
    * 序列化 （问题：有哪几种序列化的方式，哪种更好？）
    
      1. Serializable接口
      2. Parcelable接口
    
    * 匿名共享内存
    
    * RemoteCallbackList
    
      RemoteCallbackList是系统专门提供用于删除跨进程listener的接口。使用registerListener和unregisgerListener 来注册和反注册，使用beginBroadcast和finishBroadcast配对使用来通知回调。
    



#### Binder

* 原理



#### 参考资料
* 《Android开发艺术探索》
* [进程和应用生命周期](https://developer.android.google.cn/guide/components/activities/process-lifecycle)
* [Android官网 - 管理内存](https://developer.android.google.cn/topic/performance/memory-overview?hl=zh-cn)
* [Android Detail：进程篇——关于进程你需要了解这些](https://xiaozhuanlan.com/topic/2036195874)    | [Android Detail：进程篇——进程内存分配与优先级](https://juejin.cn/post/6891911483379482637)
* [Android AIDL参数中in、out、inout、oneway含义及区别](http://nicethemes.cn/news/txtlist_i6454v.html)



### 主题样式

* 知识点
  * <font color='red'> TODO</font>
* 参考资料
  * [Styles and Themes -  CodePath - 使用篇](https://guides.codepath.com/android/Styles-and-Themes)



### 编译&打包

* 知识点
  * Gradle
  * 编译
  * 混淆
  * 签名
* 参考资料
  * [浅谈Android打包流程](https://juejin.cn/post/6844903850453762055)
  * [Android APK打包流程](https://juejin.cn/post/6844903838894260238)
  * [配置构建 - Android官网](https://developer.android.google.cn/studio/build/)
  * [Android 代码混淆，到底做了什么？](https://mp.weixin.qq.com/s/zi3K7lXfSodHj6tUTLKxgg)



## APP组件知识

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

      4. 

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
  * 创建Fragment

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

      *  [`findFragmentById()`](https://developer.android.com/reference/androidx/fragment/app/FragmentManager#findFragmentById(int)) 
      *  [`findFragmentByTag()`](https://developer.android.com/reference/androidx/fragment/app/FragmentManager#findFragmentByTag(java.lang.String)) 

  * Fragment之间添加过度动画效果

    1. 设置Animation动画
       * 使用[`FragmentTransaction.setCustomAnimations()`](https://developer.android.com/reference/androidx/fragment/app/FragmentTransaction#setCustomAnimations(int, int))方法
    2. Transitions动画

  * 生命周期

    1. 使用 addToBackStack()之后的生命周期？

       <img src="https://developer.android.google.cn/images/guide/fragments/fragment-view-lifecycle.png" style="zoom:50%;" />

    2. 使用ViewPager2的生命周期

  * 与Fragment通信

    1. `Fragment` 库提供了两个通信选项：共享的 [`ViewModel`](https://developer.android.com/topic/libraries/architecture/viewmodel) 和 Fragment Result API。建议的选项取决于用例。如需与任何自定义 API 共享持久性数据，您应使用 `ViewModel`。对于包含的数据可放置在 [`Bundle`](https://developer.android.com/reference/android/os/Bundle) 中的一次性结果，您应使用 Fragment Result API。

  * ViewPager与 Fragment

    1. ViewPagerFragmentPagerAdapter 和 FragmentStatePagerAdapter
       * FragmentPagerAdapter把每一页都表现为一个Fragment，并且会被保留在fragment manager 中。
         FragmentPagerAdapter比较适合于少量静态Fragment之间的切换, 比如一套Tab。每一个用户浏览过的Fragment会被保存在内存中，即使它的view已经destroy了。这会导致 使用大量的内存，因为Fragment的实体会被保存到任意的状态。
       * FragmentStatePagerAdapter使用一个Fragment管理每个page。这个类同时也操控保存和恢复fragment的状态。
         这个类适合于有大量pages的时候，比如一个列表视图。当page不可见的时候，它们的fragment也会被销毁，只会保留被保存的状态。 这种模式比FragmentPagerAdapter 使用更少的内存，但是在切换的时候会有更多的消耗。
    2. 切换时的生命周期

  * ViewPager2 与 Fragment 

    1. Viewpager2比ViewPager的优势

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
* 参考资料
  * [官网文档](https://developer.android.google.cn/guide/fragments)
  * [深入了解ViewPager2](https://juejin.cn/post/6844904020553760782)
  * [androidx中的Fragment懒加载方案](https://www.jianshu.com/p/a34eef0e3bc9)
  * [ViewPager2中的Fragment缓存、预取机制、懒加载实现方式](https://www.jianshu.com/p/1d95e729c571)
  * [Android总结 - Fragment](https://blog.csdn.net/Siobhan/article/details/51179833?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522162461185316780261988958%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=162461185316780261988958&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_v2~rank_v29-1-51179833.pc_v2_rank_blog_default&utm_term=Fragment&spm=1018.2226.3001.4450)
  * [【背上Jetpack之Fragment】你真的会用Fragment吗？Fragment常见问题以及androidx下Fragment的使用新姿势](https://juejin.cn/post/6844904079697657863)





## 交互与布局



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
  * Adapter、ViewHolder、LayoutManager
  * 局部刷新
  * ItemDecoration
  * 动效|ItemAnimator 
  * 点击&长按
  * 下拉刷新 & 加载更多
  * Header & Footer 
  * SnapHelper
    	LinearSnapHelper
    	PagerSnapHelper
  * 进阶
    	四级缓存
* 参考资料
  * [RecyclerView - Android官网文档](https://developer.android.com/guide/topics/ui/layout/recyclerview?hl=zh_cn)
  * [深入理解 RecyclerView 的缓存机制](https://juejin.cn/post/6844904146684870669)





### WebView

* 知识点
  * 在WebView中使用JavaScript，默认是关闭的；JS和Android之间相互调用，WebView使用`addJavascriptInterface()` 来给JS添加接口；但是出于安全原因，除非全部的代码都是自己所写，不然建议使用默认的浏览器。
  * 网页导航
    * 如果想要在应用中打开链接调整，则需要给WebView设置一个WebViewClient，使用WebViewClient中的` shouldOverrideUrlLoading` 可以响应网页中的超链接，可以对请求进行拦截。使用`goBack()` 和`goForward()`可以向后或向前浏览历史记录。
* 参考资料
  * [Working with the WebView - CodePath - 使用篇](http://guides.codepath.com/android/Working-with-the-WebView) 
  * [WebView你真的熟悉吗？看了才知道](http://www.jianshu.com/p/d2f5ae6b4927)





### ImageView

* 知识点
  * 使用、属性
  * Scale Types
  * ImageView和Bitmap
  * SVG图片
* 参考资料
  * [Working with the ImageView](https://github.com/codepath/android_guides/wiki/Working-with-the-ImageView)



### Bitmap

* 知识点
  * Bitmap 与 density、dpi直接的关系
  * Bitmap内存占用
  * Bitmap加载、拉伸裁剪、保存
  * Bitmap大图加载-BitmapRegionDecoder
  * ThumbnailUtils类
  * 图片加载的缓存策略：LRUCache
  * 图片加载框架：Glide、Fresco
* 参考资料
  * 《Android开发艺术探索》
  * [Android Bitmap 面面观](https://juejin.cn/post/6844903433313452040)
  * [Android 开发绕不过的坑：你的 Bitmap 究竟占多大内存？](https://www.cnblogs.com/krislight1105/p/5203277.html)
  * [Android Bitmap变迁与原理解析（4.x-8.x）](https://juejin.cn/post/6844903608887017485)
  * [Android 高清加载巨图方案 拒绝压缩图片](https://blog.csdn.net/lmj623565791/article/details/49300989)

### Drawable

* 知识点
  * ShapeDrawable
  * StateListDrawable
  * LayerListDrawable
  * NinePatchDrawable
  * VectorDrawable
* 参考资料
  * [Drawables -  CodePath - 使用篇](https://guides.codepath.com/android/Drawables)
  * 《Android开发艺术探索》



### 窗口 window 

* 知识点
* 参考资料
  * [像360悬浮窗那样，用WindowManager实现炫酷的悬浮迷你音乐盒（上）](http://www.jianshu.com/p/95ceb0a2ed27)



### Android View事件体系

* 知识点

  * View坐标系 与 对象方法

  * 

    

* 参考资料

  * [那些你应该知道却不一定知道的——View坐标分析汇总](https://blog.csdn.net/mr_immortalz/article/details/51168278)

  * [安卓自定义View进阶-多点触控详解](http://www.gcssloop.com/customview/multi-touch.html)

    

### Android 自定义View

* 知识点
* 参考资料
  * [Android自定义View教程目录](http://www.gcssloop.com/category/customview.html)



## 动画

* 知识点
  * 动画类型（PropertyAnimation、Layout、Drawable、Fragment、Activity（overridePendingTransition、Transitions）
  * 动画监听
  * 插值器与估值器		
* 参考资料
  * [Animations -  CodePath - 使用篇](https://guides.codepath.com/android/Animations)
  * 《Android开发艺术探索》



## APP 后台任务

* 知识点
  * 决策树
    	![此决策树可帮助您确定哪个类别最适合您的后台任务](https://developer.android.google.cn/images/guide/background/task-category-tree.png)
  * 概括一下：**及时任务** 使用 Kotlin 协程、线程池、RxJava等，**延期任务**使用 WorkManager、JobSchedule，**精确任务** 使用 AlarmManager。
    ![后台任务推荐方案](IMG\后台任务推荐方案.png)
* 参考资料
  * [官网文档 - 后台任务](https://developer.android.com/guide/background)
  * [Notifications (Persistent Notices on the Dashboard) -  CodePath - 使用篇](https://github.com/codepath/android_guides/wiki/Notifications)
  * [Notification 使用](http://blog.csdn.net/siobhan/article/details/50856433)



## APP 存储&持久化

### 应用数据和文件

* 知识点
  * 
* 参考资料
  * [官网文档 - 应用数据存储](https://developer.android.google.cn/training/data-storage) 



### Preferences





### Provider

* 知识点
  * 权限
  * 创建CP
  * 读取或操作CP数据
  * 创建和使用FileProvider

* 参考资料
  * [ContentProvider 的批处理操作](https://blog.csdn.net/siobhan/article/details/54983233)



### Files

* 知识点
* 参考资料
  * [数据和文件存储概览-Android官网](https://developer.android.google.cn/training/data-storage)
  * [Android总结 - 保存数据](https://blog.csdn.net/siobhan/article/details/51145498)



### SQLite

* 知识点

  * SQLiteOpenHelper （创建、CRUD）
  * 批量操作（bultInsert、ContentProviderOperation）
  * 升级、降级（逐版本升级）
  * ORMs(数据库框架)

* 参考资料

  * [Local Databases with SQLiteOpenHelper - CodePath - 使用篇](https://guides.codepath.com/android/Local-Databases-with-SQLiteOpenHelper)

  * [绝对值得一看的Android数据库升级攻略](https://blog.csdn.net/s003603u/article/details/53942411)

    





## APP 网络操作

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
* 参考资料
  * [ Sending and Managing Network Requests (API Calls, Image Downloading) - CodePath - 使用篇](https://github.com/codepath/android_guides/wiki/Sending-and-Managing-Network-Requests)



### 文件下载

* 参考资料
  * [多线程断点下载](http://godcoder.me/tags/多线程下载/)
  * [Android快速实现文件下载（只有4行代码）](http://www.jianshu.com/p/46fd1c253701)



### Socket

* 参考资料
  * [Sending and Receiving Data with Sockets - CodePath - 使用篇](http://guides.codepath.com/android/Sending-and-Receiving-Data-with-Sockets)



### 网络框架

* [Retrofit](#Retrofit)
* [okhttp](#okhttp)



## APP架构

### 综合知识

* 参考资料
  * [彻底理解Android架构](https://zhuanlan.zhihu.com/p/23772285)
  * [官网 Android架构组件](https://developer.android.com/topic/libraries/architecture)



### Android 官方架构组件

#### LiveData



#### ViewModel



### MVVM

* 知识点
* 参考资料
  *  [如何构建Android MVVM 应用框架](https://tech.meituan.com/2016/11/11/android-mvvm.html)



### 组件化

* 知识点
  * 组件化方案
  * 页面路由
* 参考资料
  * [“终于懂了” 系列：Android组件化，全面掌握！](https://mp.weixin.qq.com/s/WSzpJXXocajJjmWgYem3fA)
  * [阿里 ARouter 全面解析，总有你没了解的](https://mp.weixin.qq.com/s/LaEbPaIKu0TNR0ygI-yOSQ)



### 插件化

* 知识点
  * class、dex基础知识
  * ClassLoader原理
    	如何hook Activity启动流程
    	双亲委派
  * 插件化原理
  * 插件化框架学习
* 参考资料
  * [Android解析ClassLoader（一）Java中的ClassLoader](https://juejin.cn/post/6844903498291625992)
  * [Android解析ClassLoader（二）Android中的ClassLoader](http://liuwangshu.cn/application/classloader/2-android-classloader.html)



### 热修复



### ASM 技术

* 概念：ASM是一种通用Java字节码操作和分析框架。它可以用于修改现有的class文件或动态生成class文件。





## APP性能优化 & 稳定性



### 综合知识

* 参考资料
  * [分享一波 Android 性能优化的总结！](https://mp.weixin.qq.com/s/MfIT_sV0xwt1UnGQWXCJIA)
  * [性能与功耗 - Android官网](https://developer.android.com/topic/performance)



### 内存知识与优化

* 知识点
  * 内存优化基础

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



### 流畅性优化|卡顿优化

* 知识点

  * 为什么会卡顿？

  * 如何监控卡顿？

    * 方案一：Looper#loop方法中的 logging.println，需要在后台开一个线程，定时获取主线程堆栈，**局限**： 只适合线下。
    * 方案二：通过Gradle Plugin+ASM，编译期在每个方法开始和结束位置分别插入一行代码，统计方法耗时。字节码插桩技术，适合线上。微信Matrix 。

  * ANR的原理

    * 哪些场景会造成ANR？

      **Service Timeout**:比如前台服务在20s内未执行完成，后台服务是10s。

      **BroadcastQueue Timeout**：比如前台广播在10s内未执行完成，后台60s。

      **ContentProvider Timeout**：内容提供者,在publish过超时10s。

      **InputDispatching Timeout**: 输入事件分发超时5s，包括按键和触摸事件。

    * 在AMS中埋炸弹和拆炸弹（非输入事件）

    * 引爆炸弹（非输入事件）

      ##### **AppErrors #appNotResponding** 

  * ANR分析法

    * “/data/anr/tarce.txt” 文件
    * 先查看主线程的状态，搜索“main”关键字
* 参考资料
  
  * [卡顿、ANR、死锁，线上如何监控？](https://juejin.cn/post/6973564044351373326)
  * [《广研Android卡顿监控系统》](https://mp.weixin.qq.com/s/MthGj4AwFPL2JrZ0x1i4fw)
  * [彻底理解安卓应用无响应机制](http://gityuan.com/2019/04/06/android-anr/)
  * [Systrace实战：彻底搞懂卡顿原理！](https://mp.weixin.qq.com/s/eij3z6wlT4k3GGQm637b-A)



### 体积优化

* 知识点
* 参考资料
  * [深入探索 Android 包体积优化（匠心制作-上）](https://juejin.cn/post/6844904103131234311)



### 稳定性知识与优化

* 知识点
  * 
* 参考资料
  * [深入探索Android稳定性优化](https://juejin.cn/post/6844903972587716621)
  * [卡顿、ANR、死锁监控方案](https://mp.weixin.qq.com/s/EzIbxeWexQv7ENDugT-UFg)



## Android安全



* 参考资料
  * [那些值得你试试的Android竞品分析工具](http://www.jianshu.com/p/ba2d9eca47a2)
  * [使用“aapt dump”查看APK内容](http://blog.sina.com.cn/s/blog_6294abe70101bkef.html)
  * [Android 反编译初探 应用是如何被注入广告的](https://blog.csdn.net/lmj623565791/article/details/53370414)



## Android JNI

* 知识点
* 参考资料
  * [为何大厂APP如微信、支付宝、淘宝、手Q等只适配了armeabi-v7a/armeabi？](https://juejin.im/post/5eae6f86e51d454ddb0b3dc6)
  * [关于Android的.so文件你所需要知道的](http://www.jianshu.com/p/cb05698a1968ß)
  * [Android深入理解JNI - 刘望舒](http://liuwangshu.cn/framework/jni/2-signature-jnienv.html)



## Andorid系统源码分析

### Android 系统启动

* 参考资料
  * [详解 Android 是如何启动的](http://www.woaitqs.cc/android/2016/06/15/how-android-launch-itself.html)
  * [Android系统启动源码分析](http://blog.csdn.net/ynztlxdeai/article/details/69675754)



### Android 消息机制

* 知识点

  * ThreadLocal
  * 消息队列MessageQueue的工作原理
  * Looper的工作原理
  * Handler的工作原理

* 参考资料

  * 《Android开发艺术探索》




### Android Input输入系统

* 知识点
  * Input输入系统
  * View的事件体系
* 参考资料
  * [Android输入系统（一）输入事件传递流程和InputManagerService的诞生](https://juejin.cn/post/6844903703200137223)
  * [Android输入系统（二）IMS的启动过程和输入事件的处理](https://juejin.cn/post/6844903717574017038)
  * [Android输入系统（三）InputReader的加工类型和InputDispatcher的分发过程](http://liuwangshu.cn/framework/ims/3-inputdispatcher.html)
  * [Android输入系统（四）输入事件是如何分发到Window的？](https://juejin.cn/post/6844903761131880455)
  * [Android4.1 InputManagerService 流程 - 自己总结](https://blog.csdn.net/Siobhan/article/details/8014246)
  * [可能是讲解Android事件分发最好的文章](https://cloud.tencent.com/developer/article/1179381)



### Android AMS系统分析

* 知识点
  * Application 应用启动流程
  * Activity扮演的角色（作用）
  * Activity的启动流程
  * Service的启动流程
  * Broadcast的启动流程
  * Provider的启动流程
* 参考资料
  * [[译]Android Application启动流程分析](https://www.jianshu.com/p/a5532ecc8377)
  * [不怕面试再问 Activity，一次彻底地梳理清楚！](https://mp.weixin.qq.com/s/FdfBfyePoX2BI5OkXtmICA)



### Android WMS系统分析

* 知识点
* 参考资料
  * [Android解析WindowManager - 刘望舒](http://liuwangshu.cn/framework/wm/3-add-window.html)
  * [Android解析WindowManagerService - 刘望舒](http://liuwangshu.cn/framework/wms/1-wms-produce.html)



### Android 显示系统

* 知识点
  * 渲染流程 vsync -> Choreographer.doFrame ->  Choreographer.doCallbacks -> ViewRootImpl.mTraversalRunnable.run -> ViewRootImpl.doTraversal() , removeSyncBarrier -> ViewRootImpl.performTraversals()
* 参考资料
  * [Android UI 渲染机制的演进，你需要了解什么？](https://mp.weixin.qq.com/s/psrDADxwl782Fbs_vzxnQg)



## Android 三方开源库

### EventBus 事件总线

* 知识点
  * 使用
  * 5种线程模型、3种事件类型
  * 观察者模式解耦
* 参考资料
  * [EventBus源码解析](https://juejin.cn/post/6844904007199113229)



### RxJava 

* 知识点
  * 常用操作符
  * 线程调度
  * 异常处理
  * Flowable背压
* 参考资料
  * [RxJava2 只看这一篇文章就够了](https://juejin.cn/post/6844903617124630535#heading-211)
  * [给初学者的RxJava2.0教程](https://www.jianshu.com/p/8818b98c44e2)
  * [史上最简单的 RxJava 源码分析](https://zhuanlan.zhihu.com/p/129889972)



### <span id="okhttp">okhttp</span>

* 知识点
  * 拦截器（责任链模式）
  * 超时重传&重定向
  * Http缓存
  * Socket连接池复用
* 参考资料
  * [Networking with the OkHttp Library - CodePath - 使用篇](https://github.com/codepath/android_guides/wiki/Using-OkHttp)
  * [Android开源框架源码鉴赏：Okhttp](https://juejin.cn/post/6844903557632622605)



### <span id="Retrofit">Retrofit</span>

* 知识点
  * 
* 参考资料
  * [深入浅出 Retrofit，这么牛逼的框架你们还不来看看？](https://mp.weixin.qq.com/s?__biz=MzA3NTYzODYzMg==&mid=2653577186&idx=1&sn=1a5f6369faeb22b4b68ea39f25020d28&scene=1&srcid=06039K4A2eGkHPxLbKED09Mk)
  * [Consuming APIs with Retrofit -  CodePath - 使用篇](https://github.com/codepath/android_guides/wiki/Consuming-APIs-with-Retrofit)



### Glide

* 知识点
  * 生命周期控制
  * 异步加载、线程切换
  * 缓存机制（四级缓存）
  * 防止OOM
  * 列表中加载图片如何设计？setTag、防止重复加载
  * BitmapPool复用
* 参考资料
  * [面试官：简历上最好不要写Glide，不是问源码那么简单](https://juejin.cn/post/6844903986412126216)
  * [Android 【手撕Glide】--Glide缓存机制](https://www.jianshu.com/p/b85f89fce019)



### LeakCanary

* 知识点
* 参考资料
  * [看完这篇 LeakCanary 原理分析，又可以虐面试官了！](https://mp.weixin.qq.com/s/1jFY_22hoWgCw3CDo2rpOA)