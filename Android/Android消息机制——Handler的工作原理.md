# Android消息机制——Handler的工作原理
Handler的工作主要包含消息的发送和接收的过程。消息的发送可以通过post的一系列方法以及send的一系列方法来实现。post的一系列方法最终是通过send的一系列方法来实现的。    

```java   
/**
 * Pushes a message onto the end of the message queue after all pending messages
 * before the current time. It will be received in {@link #handleMessage},
 * in the thread attached to this handler.
 * 
 * @return Returns true if the message was successfully placed in to the 
 *         message queue.  Returns false on failure, usually because the
 *         looper processing the message queue is exiting.
 */
public final boolean sendMessage(Message msg)
{
    return sendMessageDelayed(msg, 0);
}

/**
 * Enqueue a message into the message queue after all pending messages
 * before (current time + delayMillis). You will receive it in
 * {@link #handleMessage}, in the thread attached to this handler.
 * 
 * @return Returns true if the message was successfully placed in to the 
 *         message queue.  Returns false on failure, usually because the
 *         looper processing the message queue is exiting.  Note that a
 *         result of true does not mean the message will be processed -- if
 *         the looper is quit before the delivery time of the message
 *         occurs then the message will be dropped.
 */
public final boolean sendMessageDelayed(Message msg, long delayMillis)
{
    if (delayMillis < 0) {
        delayMillis = 0;
    }
    return sendMessageAtTime(msg, SystemClock.uptimeMillis() + delayMillis);
}

/**
 * Enqueue a message into the message queue after all pending messages
 * before the absolute time (in milliseconds) <var>uptimeMillis</var>.
 * <b>The time-base is {@link android.os.SystemClock#uptimeMillis}.</b>
 * Time spent in deep sleep will add an additional delay to execution.
 * You will receive it in {@link #handleMessage}, in the thread attached
 * to this handler.
 * 
 * @param uptimeMillis The absolute time at which the message should be
 *         delivered, using the
 *         {@link android.os.SystemClock#uptimeMillis} time-base.
 *         
 * @return Returns true if the message was successfully placed in to the 
 *         message queue.  Returns false on failure, usually because the
 *         looper processing the message queue is exiting.  Note that a
 *         result of true does not mean the message will be processed -- if
 *         the looper is quit before the delivery time of the message
 *         occurs then the message will be dropped.
 */
public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
    MessageQueue queue = mQueue;
    if (queue == null) {
        RuntimeException e = new RuntimeException(
                this + " sendMessageAtTime() called with no mQueue");
        Log.w("Looper", e.getMessage(), e);
        return false;
    }
    return enqueueMessage(queue, msg, uptimeMillis);
}


private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
    msg.target = this;
    if (mAsynchronous) {
        msg.setAsynchronous(true);
    }
    return queue.enqueueMessage(msg, uptimeMillis);
}

```   

可以看见sendMessage方法最后会执行enqueueMessage方法，把消息插入消息队列中。
然后MessageQueue的next方法就会取出这条消息给Looper，Looper拿到消息后就开始处理，最后消息由
Looper交给Hanlder处理，Hanlder就会调用dispatchMessage方法，然后回调handleMessage方法，最
后在handleMessage方法里面处理消息。

```java

/**
 * Handle system messages here.
 */
public void dispatchMessage(Message msg) {
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
回调方法：   

```java
/**
 * Callback interface you can use when instantiating a Handler to avoid
 * having to implement your own subclass of Handler.
 *
 * @param msg A {@link android.os.Message Message} object
 * @return True if no further handling is desired
 */
public interface Callback {
    public boolean handleMessage(Message msg);
}

/**
 * Subclasses must implement this to receive messages.
 */
public void handleMessage(Message msg) {
}

```

例子：  
 
```java
   Handler handler1 = new Handler(){
    @Override
    public void handleMessage(Message msg) {
        super.handleMessage(msg);
    }
};
```   
通过回调方法，消息就会到handleMessage(Message msg)中，然后在方法中处理消息。
Handler handler = new Handler(Looper looper)；
这种初始化的方式，是用handler来绑定线程。一般looper 为 Looper.getMainLooper（）


