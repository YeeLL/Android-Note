# Android消息机制——Looper的工作原理（艺术开发探索笔记）
Looper在Android消息机制中扮演消息循环者的角色，具体就是会不停的从MessageQueue中查看是否有新的消息，如果有新的消息就会处理，否则就会阻塞在那里。Handler的工作需要Looper，没有Looper的线程就会报错。通过Looper.prepare()就可以为当前的线程创建一个Looper，然后通过Looper.looper()来开启消息循环。<!--more-->   
Looper为主线程（ActivityThread）提供了创建Looper的方法——prepareMainLooper，这个方法的本质也是也是用的Looper.prepare().   
主线程也可以通过getMainLooper获取主线程的Looper。（这个方法可以在其他地方获取到主线程的Looper）   
Looper也是可以退出的，Looper提供了quit和quitsafely方法。两个的区别是quit是直接退出，quitsafely是设置一个退出标记，然后把消息队列中的已有消息处理完毕后再安全的退出。Looper退出后，通过Handler发送消息会失败。   
在子线程中手动创建了Looper，在所有的事情完成只有要调用quit方法来终止消息的循环，否则这个子线程就会一直处于等待状态，退出Looper以后，这个线程就会立即终止，因此在不需要的时候终止Looper。   
Looper的loop方法是一个死循环，唯一的跳出循环的方法就是MessageQueue的next方法返回null。当Looper的quit方法被调用的时候Looper就会调用MessageQueue的quit方法，当消息队列被标记为退出状态时，他的next方法就会返回null。也就是说，Looper必须退出，否者loop方法就会无限循环下去。loop会调用MessageQueue的next方法，但是next方法本身就是一个阻塞方法，所以loop就会一直阻塞在那里。  
只有UI线程也就是主线程会默认调用Looper.prepare()和Looper.loop();

---

### Looper.loop()方法：
```java
/**
 * Run the message queue in this thread. Be sure to call
 * {@link #quit()} to end the loop.
 */
public static void loop() {
    final Looper me = myLooper();
    if (me == null) {
        throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
    }
    final MessageQueue queue = me.mQueue;

    // Make sure the identity of this thread is that of the local process,
    // and keep track of what that identity token actually is.
    Binder.clearCallingIdentity();
    final long ident = Binder.clearCallingIdentity();

    for (;;) {//死循环
        Message msg = queue.next(); // might block  next方法本身也是一个阻塞方法
        if (msg == null) {
            // No message indicates that the message queue is quitting.
            return;
        }

        // This must be in a local variable, in case a UI event sets the logger
        Printer logging = me.mLogging;
        if (logging != null) {
            logging.println(">>>>> Dispatching to " + msg.target + " " +
                    msg.callback + ": " + msg.what);
        }

        msg.target.dispatchMessage(msg);

        if (logging != null) {
            logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);
        }

        // Make sure that during the course of dispatching the
        // identity of the thread wasn't corrupted.
        final long newIdent = Binder.clearCallingIdentity();
        if (ident != newIdent) {
            Log.wtf(TAG, "Thread identity changed from 0x"
                    + Long.toHexString(ident) + " to 0x"
                    + Long.toHexString(newIdent) + " while dispatching to "
                    + msg.target.getClass().getName() + " "
                    + msg.callback + " what=" + msg.what);
        }

        msg.recycleUnchecked();
    }
}
```
