# ANR

### 什么是ANR

ANR全称Application Not Responding，意思就是程序未响应。如果一个应用无法响应用户的输入，系统就会弹出一个ANR对话框，如下图所示,用户可以自行选择继续等待亦或者是停止当前程序。  
出现场景

* 主线程被IO操作（从4.0之后网络IO不允许在主线程中）阻塞。
* 主线程中存在耗时的计算
* 主线程中错误的操作，比如Thread.wait或者Thread.sleep等
Android系统会监控程序的响应状况，一旦出现下面两种情况，则弹出ANR对话框

* 应用在5秒内未响应用户的输入事件（如按键或者触摸）
* BroadcastReceiver未在10秒内完成相关的处理   



  