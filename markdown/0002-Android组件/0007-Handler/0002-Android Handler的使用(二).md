#### 一、 Handler与线程的关系 
Handler在默认情况下，实际上它和调用它的Activity是处于同一个线程的。 
例如在Handler的使用（一）的示例1中，虽然声明了线程对象，但是在实际调用当中它并没有调用线程的start()方法，而是直接调用当前线程的run()方法。 
通过一个例子来证实一下 
示例1：一个Android应用程序，在Activity中创建Handler和线程对象，并且在Activity的onCreate()方法中输出当前线程的id和名字，然后在线程对象的run方法中也打印输出下当前线程的id和名字。如果说，Activity输出的结果与线程对象输出的结果是一样的，那么就表示它们使用的是同一个线程。 
下面是Activity代码： 
```  
import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
public class HandlerTwo extends Activity {
	[Tags]/** Called when the activity is first created. */
	Handler handler = new Handler();
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// 在设置布局文件之前先调用post方法，
		// 表示在执行完线程之后才会显示布局文件中的内容，而线程中又设置了休眠10秒钟，
		// 所以最终效果为，先显示应用程序主界面，等待10秒钟之后才显示布局文件中的内容
		handler.post(r);
		setContentView(R.layout.main);
		System.out.println("activity id--->" + Thread.currentThread().getId());
		System.out.println("activity name--->
				+ Thread.currentThread().getName());
	}
	Runnable r = new Runnable() {
		public void run() {
			// 输出当前线程的id和name
			// 如果这里输出的线程id、name与上面onCreate()方法中输出的线程id、name相同的话，
			// 那么则表示，他们使用的是同一个线程
			System.out.println("runnable_id--->
					+ Thread.currentThread().getId());
			System.out.println("runnable_name--->
					+ Thread.currentThread().getName());
			try {
				Thread.sleep(10000); // 让线程休眠10秒
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	};
}
```
根据结果可以看出，两个输出的id和name都相同，它们使用的是同一个线程。 
现在将Activity中的代码修改一下，新建一个线程Thread，然后调用线程的start()方法，再观察一下控制台的输出结果。 
这里只要将上面的代码稍微修改一下就可以了。
1、先将handler.post(r)注释掉。
2、再添加下面两句代码就OK了。
```  
//handler.post(r); 
Thread t = new Thread(r); 
t.start(); 
```
从这个输出结果中可以看出，这次线程对象的id、name与activity里的线程id、name完全不一样了，由此可见，它们现在使用的不是同一个线程。 
这个例子中还掩饰了一个效果，就是平时我们是将Handler的post()方法放在setContentView(R.layout.main)这个方法之后调用，将设置完布局之后再执行其他的操作，而在这个例子中，是将Handler的post()方法放在setContent()方法之前调用，而post里传递的线程对象的run()方法呢，又执行了休眠线程10秒钟，所以运行实现的效果会是，当程序运行后，首先Activity上没有任何内容，过来10秒之后，才会显示Activity里的内容。
#### 二、 Bundle和如何在新线程中处理消息 
首先介绍一下Bundle： 
Bundle它是一个以string为键，可以由其他数据类型作为值的一个mapping，相当于把数据当成一个包。在初学的阶段可以将它当成特殊的一个HashMap对象，不过HashMap的键和值都是Object类型的，而Bundle的键却是String类型。 
通过一个例子来使用一下Bundle和如何在新线程中处理消息.
示例2：一个Android应用程序，先打印Activity当前使用的线程id，然后再创建一个新线程，使用Bundl存储值，最后打印出线程的id和Bundle中存储的值。 