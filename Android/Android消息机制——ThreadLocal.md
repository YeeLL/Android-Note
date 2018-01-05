# Android消息机制——ThreadLocal
ThreadLocal是一个线程内部的数据存储类，通过它可以在指定的线程中存储数据，数据存储以后，只有在指定线程中可以获取到存储的数据，对于其他线程来说则无法获取到数据。
在日常开发中用到这个类的地方为:   
某些数据是以线程为作用域并且不同线程具有不同的数据副本的时候。 <!--more-->    
列如： 
 
```java    

public class MainActivity extends AppCompatActivity {
	private ThreadLocal<String> m = new ThreadLocal<>();

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		m.set("主线程");
		m.remove();
		new Thread("子线程1") {
			@Override
			public void run() {
			super.run();
			m.set("子线程1");

			Log.e("子线程1", m.get());
			}
		}.start();

		new Thread("子线程2") {
			@Override
			public void run() {
			super.run();
			m.set("子线程2");
			Log.e("子线程2", m.get());
			}
		}.start();

		Log.e("主线程", m.get());
	}
}
```   
结果：   
02-15 10:43:01.413 5953-5997/kyle.yee.com.yiaccount E/子线程1: 子线程1   
02-15 10:43:01.413 5953-5953/kyle.yee.com.yiaccount E/主线程: 主线程   
02-15 10:43:01.414 5953-5998/kyle.yee.com.yiaccount E/子线程2: 子线程2   
结论：   
相同的对象在不同的类中操作的不同的数据副本。相当于线程自己在操作自己线程的数据。其他线程无法访问。
