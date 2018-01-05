
# 线程和线程池 handlerThread

进程:资源分配的最小单位。
线程：最小调度单位。
一个线程只能有一个进程，一个进程可以由无数的线程。
创建线程的3种方式：
1.直接继承Thread类。
2.实现Runnable接口。
3. 通过Callable和Future创建线程。

现在进入正题 线程池：
首先什么叫线程池。线程池就是在一个多线程的应用中，有一个线程的集合。然后在需要执行新任务时重用这些线程，而不是重新开一个。

handlerThread就是一个线程实现了handler机制。

创建线程池的四种方式：
1.newFixedThread(int size) 创建固定线程数量的线程池。超出的线程会在队列中等待。   
例子：   

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executor;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.FutureTask;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;

public class ExecutorStudy {

    public static void main(String[] args) {

        //创建一个固定长度为5的线程池
        ExecutorService fixed = Executors.newFixedThreadPool(5);

        for (int i = 0; i < 20; i++) {
            fixed.execute(new Runnable() {

                @Override
                public void run() {
                    // TODO Auto-generated method stub
                    System.out.println(Thread.currentThread());
                }
            });
        }

    }

}
```

2.newCachedThread() 创建一个缓存线程池，当没有可用线程时就创建，如果任务来了，有可用线程就复用。   
例子：   
```java
       //创建一个缓存线程池
              ExecutorService cached = Executors.newCachedThreadPool();
              for (int i = 0; i < 20; i++) {
                     cached.execute(new Runnable() {
                           @Override
                           public void run() {
                                  // TODO Auto-generated method stub
                                  System.out.println(Thread.currentThread());
                           }
                     });
              }
              fixed.shutdown();
```


可以看出线程时一直增长的，但是当前面使用过的线程空闲时又会给安排新的任务。

3.newSingleThread() 创建一个单独的线程池， 保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。
例子：

```java 

       // 创建一个单独的线程池
 ExecutorService excutor = Executors.newSingleThreadExecutor();
              for (int i = 0; i < 20; i++) {
                     excutor.execute(new Runnable() {
                           @Override
                           public void run() {
                                  // TODO Auto-generated method stub
                                  System.out.println(Thread.currentThread());
                           }
                     });
              }
              excutor.shutdown();
```
 

4.newScheduledThread()创建一个定长线程池，支持定时及周期性完成任务。   
例子：   

```java
// 创建一个延时为3秒的线程池
              ScheduledExecutorService scheduled = Executors
                           .newScheduledThreadPool(5);
              for (int i = 0; i < 10; i++) {
                     scheduled.schedule(new Runnable() {
                           @Override
                           public void run() {
                                  // TODO Auto-generated method stub
                                   System.out.println(Thread.currentThread());
                           }
                     }, 3, TimeUnit.SECONDS);
              }
              scheduled.shutdown();
```


总结：   
          合理使用线程池可以使得程序的资源消耗更低，程序运行的更流畅。
          