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
    * 在配置变更期间保留对象（onSaveInstanceState()、ViewModel 和持久存储）
    * 自行处理配置变更
* 参考资料
  * [官网 - 处理配置变更](https://developer.android.google.cn/guide/topics/resources/runtime-changes)



### 进程和IPC

* 知识点
  * 进程的优先级（前台进程、可见进程、服务进程、缓存进程）
  * APP多进程实现
    * 如何开启多进程？如何实现私有进程？
    * 多进程引发的问题
  * 进程保活
    * lowmemorykiller   、 oom_adj 
    * 提高进程优先级
    * 杀死之后再次拉起
  * 进程间通信方式
    * 使用Bundle
    * 使用文件共享
    * 使用Socket
    * 使用Binder（使用AIDL、使用Messenger、使用ContentProvider、Binder线程池）
  * IPC通信Binder
    * AIDL（如何实现、参数 in、out、inout、参数oneway）
    * 序列化
    * 匿名共享内存

* 参考资料
  * 《Android开发艺术探索》
  * [进程和应用生命周期](https://developer.android.google.cn/guide/components/activities/process-lifecycle)
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
  * Activity的生命周期和启动模式
  * IntentFilter的匹配规则
* 参考资料
  * 《Android开发艺术探索》
  * [不怕面试再问 Activity，一次彻底地梳理清楚！](https://mp.weixin.qq.com/s/FdfBfyePoX2BI5OkXtmICA)



### Fragment

* 知识点
  * 生命周期
  * ViewPager和ViewPager2
  * Fragment动画
  * 与Activity通信
* 参考资料
  * [官网文档](https://developer.android.com/guide/fragments)
  * [Android总结 - Fragment](https://blog.csdn.net/Siobhan/article/details/51179833?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522162461185316780261988958%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=162461185316780261988958&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_v2~rank_v29-1-51179833.pc_v2_rank_blog_default&utm_term=Fragment&spm=1018.2226.3001.4450)
  * [【背上Jetpack之Fragment】你真的会用Fragment吗？Fragment常见问题以及androidx下Fragment的使用新姿势](https://juejin.cn/post/6844904079697657863)



### Broadcast

* 知识点
  * 系统广播的变更
  * 接收广播
  * 发送广播
  * 发送、接收广播如何限制
  * 注意事项（安全权限、生命周期、性能效率）
* 参考资料
  * [Broadcast - 官网文档](https://developer.android.com/guide/components/broadcasts) 



### Service

* 知识点
  * 启动方式（Start、Binder）
  * 前台服务
  * 绑定服务（会抛异常）
  * AIDL
* 参考资料
  * [Service - 官网文档](https://developer.android.com/guide/components/services) 
  * [Service自己的总结]( https://blog.csdn.net/Siobhan/article/details/51315974)  



### Permission

* 知识点
* 参考资料



## 交互与布局



### include merge ViewStub

* 知识点
  * 
* 参考资料
  * [Android抽象布局——include、merge 、ViewStub](http://blog.csdn.net/xyz_lmn/article/details/14524567)



### ViewSwitcher、TextSwitcher、ImageSwitcher

* 知识点
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
    	为什么要做内存优化
    	什么是Dalvik和ART
    	什么是低杀
    	图片对内存有什么影响
    	什么是内存泄露
    	什么是内存抖动
  * 内存优化方法
    	Memory profile
    	MAT
    	LeakCanary
    	怎么监听和获取系统内容
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