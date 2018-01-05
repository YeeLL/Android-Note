
# Activity生命周期  

Activity是Android最重要的组件，因为它是与用户交互的。所以我们要弄明白Activity的生命周期。   
Activity的生命周期有两种情况，第一种是正常情况下的创建，销毁。第二种是Activity被内存回收的情况。   

#### 正常情况下的生命周期     
   1.onCreate   
     表示Activity正在被创建,在这个方法中我们可以做一些初始化的工作，比如setContentView去加载资源布局、初始化Activity所需的数据。   
     2.OnRestart   
      表示Activity正在重新启动，Activity由不可见重新变为可见状态。这个过程一般是用户导致的。   
     3.OnStart   
       表示Activity正在被启动，即将开始，这个时候Activity已经可见，但是还没有出现到前台，无法和用户交互。   
     4.OnResum   
       表示Activity已经可见，并且出现在前台并开始活动，可以与用户用户做一些交互。它与OnStart都表示Activity可见。   
     5.OnPause   
        表示Activity正在停止，此时可以做一些操作，但是不能太耗时，因为新的Activity的onStart要等OnPause执行完才能执行。   
     6.OnStop   
        表示Activity即将停止，可以做一些重量级的回收操作，同样不能太耗时。   
     7.OnDestory   
        表示Acitivity即将被销毁，这个是Activity的最后一个毁掉，在这里我们可以做一些回收工作和最终的资源的释放。   

问题1：onStart 和onResum ,OnPause和OnStop从描述上来看差不多，对我们来说有什么实质上的不同呢？   
在我的理解中实质上的不同就是 能否与用户进行交互。onResum,OnPause都是在前台，而其他两个可见但是不在前台。   

#### 异常情况下的生命周期。   
有几种情况     
##### 1.资源相关的系统废纸发生改变导致Activity被杀死并重建。      
最典型的情况就是横竖屏，当横竖屏切换的时候，Activity会被销毁并重新创建。   
在这种情况下Android会先调用它的onSaveInstanceState方法，保存当前状态。并且会把onSaveInstanceState保存的Bundle对象作为参数传给onRestoreInstanceState和onCreate方法。      
我们可以从这两个方法中取出之前保存的数据并回复。   

问题2:onRestoreInstanceState和onCreate方法既然都可以取出之前保存的数据，那么为什么还会有两个方法。   
首先调用时机不同，onRestoreInstanceState是在onStart之后调用。   
第二onRestoreInstanceState方法一旦被调用，那么Bundle一定是有值得。而OnCreate方法每次还需要判断是否为空。   

##### 2.资源内存不足导致低优先级的Activity被杀死   
这种情况下的资源存储和恢复过程和第一种情况是相同的。主要讲述下Activity的优先级情况。由高到低分为以下三种。   
1.前台Activity——正在和用户交互。优先级最高。   
2.可见但非前台Activity——比如弹出一个对话框，导致Activity可见但是位于后台无法和用户直接交互。   
3.后台Activity——已经被暂停的Activity，优先级最低。   

