# Android的线程和线程池——AsyncTask
 线程在Android中是一个很重要的概念，从用途上来说线程分为主线程和子线程，主线程主要处理和界面相关的事情，而子线程往往用于执行耗时操作。在操作系统中，线程是操作系统调度的最小单元，同时线程又是一种受限的系统资源，即线程不可能无限制地生产，并且线程的创建和销毁都会有相应的开销。   
     AsyncTask 是一种轻量级的异步任务类，它可以在线程池中执行后台任务，然后把执行的进度和最终结果传递给主线程中更新UI。但是AsyncTask并不适合进行特别好使的后台任务，对于特别好使的任务开说，建议使用线程池。<!--more-->   
     AsyncTask提供4个核心方法：   
1.onPreExecute(),在主线程中执行，在异步任务执行前，此方法会被调用。   
2.doInBackground(Params... params),此方法用于执行异步任务。在此方法中可以通过    
publishProgress方法来更新任务进度，publishProgress方法会调用    onProgressUpdate方法。此外需要把结果返回给onPostExecute方法。
3.onProgressUpdate（Progress...values）   
4.onPostExcute(Result result),在主线程中执行。

AsyncTask再具体的使用过程中也有一些条件限制的，主要有如下几点：   
1.必须在主线程中加载。   
2.必须在主线程中创建。   
3.execute方法必须在UI线程调用。   
4.不要在程序中调用4个方法。   
5.一个AsyncTask对象只能执行一次。   
  