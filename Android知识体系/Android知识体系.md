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



## APP 后台任务





## APP 存储&持久化





## APP 网络操作





## APP架构





## Andorid系统源码分析





## Android 三方开源库