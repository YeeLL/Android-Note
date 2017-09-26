# 在Android Sudio中使用Uiautomator 做自动化测试

今天早些时候看见公众号推送一篇文章[还在手动找Bug?快用自动化测试提高效率吧！](https://mp.weixin.qq.com/s?__biz=MzIwMzYwMTk1NA==&mid=2247487150&idx=1&sn=2f25036ea03e4599d60aa9fc57e1a55e&chksm=96cdafe3a1ba26f5c25e0f309df042f5010a52384f12354b16b56a75e48bd4dbff51ec7c619d&mpshare=1&scene=23&srcid=0926ocggfEd8f4un8Pv4y2sl#rd)决定尝试一下，毕竟自动化测试还是Android程序员必须的技能。    
看完公众号的第一个想法肯定就是尝试一下，当然在尝试之前还是需要了解一下这个是什么？然后才是怎么使用？   
### [Uiautomator](https://developer.android.com/topic/libraries/testing-support-library/index.html)是什么?   
UI Automator 测试框架提供了一组 API 来构建 UI 测试，用于在用户应用和系统应用中执行交互。利用 UI Automator API，您可以执行在测试设备中打开“设置”菜单或应用启动器等操作。UI Automator 测试框架非常适合编写黑盒自动化测试，其中的测试代码不依赖于目标应用的内部实现详情。   
### UI Automator 测试框架的主要功能包括：   
用于检查布局层次结构的查看器。如需了解详细信息，请参阅 UI Automator 查看器。    
在目标设备上检索状态信息并执行操作的 API。如需了解详细信息，请参阅访问设备状态。    
支持跨应用 UI 测试的 API。如需了解详细信息，请参阅 UI Automator API。    
要求 Android 4.3（API 级别 18）或更高版本。  
  
### UI Automator 查看器   
uiautomatorviewer 工具提供了一个方便的 GUI，可以扫描和分析 Android 设备上当前显示的 UI 组件。您可以使用此工具检查布局层次结构，并查看在设备前台显示的 UI 组件属性。利用此信息，您可以使用 UI Automator（例如，通过创建与特定可见属性匹配的 UI 选择器）创建控制更加精确的测试。   

uiautomatorviewer 工具位于 <android-sdk>/tools/ 目录中。

### 访问设备状态   
UI Automator 测试框架提供了一个 UiDevice 类，用于在目标应用运行的设备上访问和执行操作。您可以调用其方法来访问设备属性，如当前屏幕方向或显示尺寸。UiDevice 类还可用于执行以下操作：   

* 更改设备旋转   
* 按 D-pad 按钮   
* 按“返回”、“主屏幕”或“菜单”按钮   
* 打开通知栏   
* 对当前窗口进行屏幕截图   


### Gradle 
```java      
dependencies {
  androidTestCompile 'com.android.support.test:runner:0.4'   
  // Set this dependency to use JUnit 4 rules
  androidTestCompile 'com.android.support.test:rules:0.4'
  // Set this dependency to build and run Espresso tests
  androidTestCompile 'com.android.support.test.espresso:espresso-core:2.2.1'
  // Set this dependency to build and run UI Automator tests
  androidTestCompile 'com.android.support.test.uiautomator:uiautomator-v18:2.1.2'
}
```

###具体使用
#### 1.打开uiautomatorviewer工具得到如图

![图1](/Users/sxky/Desktop/uiautoview1.png)   

这个就是我们要使用的神奇的工具，当我们连接上设备后，点击左上方第二个按钮就会得到如下图所示的内容：

![图2](/Users/sxky/Desktop/uiaotuview2.png)
看到的内容分3块：   
1.左边的界面显示区域。   
2.右上方的布局显示区域。   
3.右下方的当前空间具体信息显示区域。    

成功后 我们打开Android Studio找到自己的项目，在androidTest下创建自己的测试类继承TestCase就能开始编写自己的测试代码了。
![图3](/Users/sxky/Desktop/autoview3.png)

创建后创建自己的testA（这里我自己的叫这个）方法，然后通过以下的API 进行操作：    
[UiCollection](https://developer.android.com/reference/android/support/test/uiautomator/UiCollection.html)：枚举容器的 UI 元素以便计算子元素个数，或者通过可见的文本或内容描述属性来指代子元素。   
[UiObject](https://developer.android.com/reference/android/support/test/uiautomator/UiObject.html)：表示设备上可见的 UI 元素。   
[UiScrollable](https://developer.android.com/reference/android/support/test/uiautomator/UiScrollable.html)：为在可滚动 UI 容器中搜索项目提供支持。   
[UiSelector](https://developer.android.com/reference/android/support/test/uiautomator/UiSelector.html)：表示在设备上查询一个或多个目标 UI 元素。   
[Configurator](https://developer.android.com/reference/android/support/test/uiautomator/Configurator.html)：允许您设置运行 UI Automator 测试所需的关键参数。   

```java
public class UiTest extends TestCase {

    public void testA() throws UiObjectNotFoundException {
        Instrumentation instrumentation = InstrumentationRegistry.getInstrumentation();

        UiDevice uiDevice = UiDevice.getInstance(instrumentation);

        Context context = instrumentation.getContext();
        //自己项目的包名
        Intent intent = context.getPackageManager().getLaunchIntentForPackage("com.hencoder.hencoderpracticedraw7");
        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);
        context.startActivity(intent);

        //通过UI Automator Viewer 得到的控件id
        UiSelector upAnimateBtn = new UiSelector().resourceId("com.hencoder.hencoderpracticedraw7:id/animateBt");
        UiObject btn = uiDevice.findObject(upAnimateBtn);

        btn.click();

        UiObject viewPager = uiDevice.findObject(new UiSelector().resourceId("com.hencoder.hencoderpracticedraw7:id/pager"));

        UiObject hsvAnimator = uiDevice.findObject(new UiSelector().resourceId("com.hencoder.hencoderpracticedraw7:id/animateBt"));


        UiScrollable titlebar = new UiScrollable(new UiSelector().className("android.widget.HorizontalScrollView"));
        //通过UI Automator Viewer 得到的控件tet
        UiObject Hsv = titlebar.getChildByText(new UiSelector().className("android.widget.TextView"), "HsvEvaluator");
        UiObject ofObject = titlebar.getChildByText(new UiSelector().className("android.widget.TextView"), "ofObject()");
        for (int i = 0; i < 5; i++) {

            ofObject.click();
            hsvAnimator.click();

            Hsv.click();
            hsvAnimator.click();
        }
    }
}
```


编写完成后可以直接点击如下图的地方直接运行测试用例：

![图4](/Users/sxky/Desktop/autoview4.png)

