# Android Application类
Android中提供了Application这样一个类。看一下Android官方文档对此类的解释：
> Base class for those who need to maintain global application state.   
You can provide your own implementation by specifying its name in your AndroidManifest.xml's <application> tag,
which will cause that class to be instantiated for ou when the process for your application/package is created.   
   

大概意思就是：需要为应用程序提供全局变量，在AndroidManifest.xml中指定所实现的Application子类；<!--more-->   
当你的Application进程被建立时，此类被实例化；   

文档解释中也提到，实现Application子类并不是必须的；
就是程序运行还是有Application概念的但不是核心，一个Application是一个进程，Application为整个程序提供Context； 此类使用非常简单；      

[http://blog.csdn.net/maxleng/article/details/5621345](http://blog.csdn.net/maxleng/article/details/5621345)

用法：   
在application中初始化或者定义变量和方法，这些变量和方法可以全局使用。
例子：
   
   
```java
public class test extends Application {

    private String name = "yixin";//可以全局使用

    @Override
    public void onCreate() {
        super.onCreate();
    }

    /**
     *  可以全局使用 在Activity中getApplication（）获取实例
     *  然后就可以调用application中的对象和方法 并且是全局的
     * @return
     */
    public String getName() {
        return name;
    }
}

```   

当然还需要在AndroidManifest中去申明:   

```xml

<application
android:name=".app.test"
android:allowBackup="true"
android:largeHeap="true"
android:icon="@drawable/ic_launcher"
android:label="@string/app_name">

```   


