一个从service播放音乐的音乐播放器，应被设置为前台运行，因为用户会明确地注意它的运行．在状态栏中的通知可能会显示当前的歌曲并且允许用户启动一个activity来与音乐播放器交互。
```  
Notification notification = new Notification(R.drawable.icon, getText(R.string.ticker_text),
System.currentTimeMillis());
Intent notificationIntent = new Intent(this， ExampleActivity.class);
PendingIntent pendingIntent = PendingIntent.getActivity(this, 0, notificationIntent, 0);
notification.setLatestEventInfo(this, getText(R.string.notification_title),
getText(R.string.notification_message),pendingIntent);
startForeground(ONGOING_NOTIFICATION,notification);
```
要请求你的service运行于前台，调用startForeground()．此方法有两个参数：一个整数唯一的标识一个通知，和这个用于状态栏的通知，例如：
要从前台移除service，调用stopForeground()。这个方法有boolean型参数，表明是否也从状态栏删除对应的通知．这个方法不会停掉service。然而，如果你停止了正在前台运行的service，这个通知也会被删除。
注意：方法startForeground()和方法stopForeground()是从Android2.0 (API Level 5)引入的。为了在早期版本是于前台运行你的service，你必须使用以前的那个setForeground()方法—见startForeground()的API文档查看如何提供与旧版本的兼容性。
#### 管理Service的生命期
一个service的生命期比一个activity要简单得多。然而，你依然需要密切关注你的service是如何被创建又是如何被销毁的，因为一个service可以运行于后台而用户看不到它。
service的生命期—从它被创建到它被销毁—有两条路可走：
#### 一个＂启动的＂service
在其它组件调用startService()时创建。然后service就长期运行并且必须调用stopSelf()自己停止自己。另一个组件也可以调用stopService()来停止它。当service停止后，系统就销毁它。
#### 一个绑定的service
当另一个组件(一个客户端)调用bindService()时创建。然后客户端通过一个IBinder接口与service通信。客户端可以调用unbindService()停止通信。多个客户端可以绑定到同一个service并且当所有的客户端都解除绑定后，系统就销毁掉这个service。(service不需停止自己。)
这两条路并不是完全分离的。也就是，你是可以绑定到用startService()启动的service的。例如，一个后台音乐service在通过传入指明要播放的音乐的intent来调用startService()后启动。之后，当用户想对播放器进行一些操作或要获取当前歌曲的信息时，一个activity可以通过调用bindService()绑定到service。在此情况下，stopService()或stopSelf()不会真正的停止service，除非所有的客户端都取消绑定了。
#### 实现生命期回调方法
就像activity，service也具有生命期回调方法，用它们你可以监视service的状态的变化并且在合适的时机做一些工作。下面的框架代码演示了每个生命期方法的实现：
```  
import android.app.Service;
import android.content.Intent;
import android.os.IBinder;
public class ExampleService extends Service {
	int mStartMode; // 表明在service被杀后的行为
	IBinder mBinder; // 客户端绑定到的接口
	boolean mAllowRebind; // 表明onRebind是否应被使用
	@Override
	public void onCreate() {
		// The service is being created
	}
	@Override
	public int onStartCommand(Intent intent, int flags, int startId) {
		// service 正在启动,在调用startService()期间被调用
		return mStartMode;
	}
	@Override
	public IBinder onBind(Intent intent) {
		// 一个客户端通过bindService()绑定到这个service
		return mBinder;
	}
	@Override
	public boolean onUnbind(Intent intent) {
		// 所有的客户端使用unbindService()解除了绑定
		return mAllowRebind;
	}
	@Override
	public void onRebind(Intent intent) {
		// 一个客户端在调用onUnbind()之后,正使用bindService()绑定到service
	}
	@Override
	public void onDestroy() {
		// service不再被使用并将被销毁
	}
}
```
注：不像activity的生命期回调方法们，你不需要调用父类的相应实现。
![img](P)  
图 service的生命期。
左图显示了用startService()创建的service的生命期，右图显示了用bindService()创建的service的生命期。
通过实现这些方法们，你可以监视service生命期的两个嵌套循环：
service的一生介于调用onCreate()的时间和onDestroy()返回的时间。就像activity，service在onCreate()中做初始化工作并且onDestroy()中释放所有资源。例如，一个音乐播放service可以在onCreate()中创建音乐播放的线程，之后在onDestroy()中停止这个线程。
onCreate()和onDestroy()方法被所有的service调用，不管它们通过startService()还是bindService()创建。
service的活动生命期开始于onStartCommand()或onBind()被调用时．每个方法各自处理传入的Intent。
如果service是＂启动的＂，活动生命期就结束于整个生命期的结束时(即使onStartCommand()返回后，service依然处于活动状态)。如果是一个绑定的service，它的活动生命期在onUnbind()返回后结束。
注：尽管一个＂启动的＂service在调用stopSelf()或stopService()时结束，但并没有单独的回调对应这些停止方法(没有类似于onStop()的回调)。所以，除非service被绑定到一个客户端，系统就会在停止时销毁service—onDestroy()是唯一收到的回调。
尽管图示分开了通过startService()和bindService()创建的service，但记住任何service，不管它是怎样启动的，都是可能允许绑定的。所以一个从onStartCommand()启动的service(客户端调用了startService())仍可以接收onBind()调用(当客户端调用bindService()时)。