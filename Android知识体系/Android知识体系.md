# Android知识体系

## 1. APP 结构

### Context 

* 知识点
  * Context的使用
  * Context的继承关系
  * Context功能
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
  * [Styles and Themes - code path - 使用](https://guides.codepath.com/android/Styles-and-Themes)



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
  * [官网文档](https://developer.android.com/guide/components/broadcasts) 



### Service

* 知识点
  * 启动方式（Start、Binder）
  * 前台服务
  * 绑定服务（会抛异常）
  * AIDL
* 参考资料
  * [官网文档](https://developer.android.com/guide/components/services) 
  * [Service自己的总结]( https://blog.csdn.net/Siobhan/article/details/51315974)  



### Permission

* 知识点
* 参考资料





## 交互与布局



### ConstraintLayout

* 知识点
* 参考资料

### RecyclerView

* 知识点
  * Adapter、ViewHolder、LayoutManager
  * ItemDecoration
  * 动效|ItemAnimator 
  * 点击&长按
  * 下拉刷新 & 加载更多
  * Header & Footer 
  * SnapHelper
    	LinearSnapHelper
    	PagerSnapHelper
  * 进阶
    	三级缓存
* 参考资料
  * [RecyclerView - Android官网文档](https://developer.android.com/guide/topics/ui/layout/recyclerview?hl=zh_cn)
  * [深入理解 RecyclerView 的缓存机制](https://juejin.cn/post/6844904146684870669)





### WebView







### ImageView

* 知识点
  * 使用、属性
  * Scale Types
  * ImageView和Bitmap
  * SVG图片
* 参考资料
  * [Working with the ImageView](https://github.com/codepath/android_guides/wiki/Working-with-the-ImageView)



### Drawable

* 知识点
  * ShapeDrawable
  * StateListDrawable
  * LayerListDrawable
  * NinePatchDrawable
  * VectorDrawable
* 参考资料
  * [Drawables - code path](https://guides.codepath.com/android/Drawables)
  * 《Android开发艺术探索》



## 动画

* 知识点
  * 动画类型（PropertyAnimation、Layout、Drawable、Fragment、Activity（overridePendingTransition、Transitions）
  * 动画监听
  * 插值器与估值器		
* 参考资料
  * [Animations - code path](https://guides.codepath.com/android/Animations)
  * 《Android开发艺术探索》



## APP 后台任务

* 知识点
  * 决策树
    	![此决策树可帮助您确定哪个类别最适合您的后台任务](https://developer.android.google.cn/images/guide/background/task-category-tree.png)
  * 概括一下：**及时任务** 使用 Kotlin 协程、线程池、RxJava等，**延期任务**使用 WorkManager、JobSchedule，**精确任务** 使用 AlarmManager。
    ![image-20210627182233527](C:\Users\shenj\AppData\Roaming\Typora\typora-user-images\image-20210627182233527.png)
* 参考资料
  * [官网文档 - 后台任务](https://developer.android.com/guide/background)
  * [Notifications (Persistent Notices on the Dashboard) - code path](https://github.com/codepath/android_guides/wiki/Notifications)
  * [Notification 使用](http://blog.csdn.net/siobhan/article/details/50856433)



## APP 存储&持久化





## APP 网络操作





## APP架构





## Andorid系统源码分析

### Android Input输入系统

* 知识点
  * 
* 参考资料
  * [Android输入系统（一）输入事件传递流程和InputManagerService的诞生](https://juejin.cn/post/6844903703200137223)
  * [Android输入系统（二）IMS的启动过程和输入事件的处理](https://juejin.cn/post/6844903717574017038)
  * [Android输入系统（三）InputReader的加工类型和InputDispatcher的分发过程](http://liuwangshu.cn/framework/ims/3-inputdispatcher.html)
  * [Android输入系统（四）输入事件是如何分发到Window的？](https://juejin.cn/post/6844903761131880455)
  * [Android4.1 InputManagerService 流程](https://blog.csdn.net/Siobhan/article/details/8014246)





## Android 三方开源库