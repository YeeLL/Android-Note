# Android消息机制——总结
Android消息处理机制主要就是消息的传递机制，通过创建消息队列，把消息放入消息队列中，通过Looper获取消息，并通过handler处理消息的过程。
整个过程最重要的就是Looper的工作机制，Looper内部通过调用MessageQueue的next方法来获取消息，得到消息后便通过Handler的dispatchMessage方法
处理消息，dispatchMessage方法内部判断消息是否为空，如果消息不为空，回调方法handleMessage不为空，就会把消息传递到回调方法中，然后用户再来处理这个消息。
整个过程MessageQueue起到存储的作用，Looper就是一个操作者，而handler就是一个用户与数据的中间件。
   
      