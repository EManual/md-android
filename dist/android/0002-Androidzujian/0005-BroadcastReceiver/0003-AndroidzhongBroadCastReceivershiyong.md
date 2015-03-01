在Android中，Broadcast是一种广泛运用的在应用程序之间传输信息的机制。而BroadcastReceiver是对发送出来的 Broadcast进行过滤接受并响应的一类组件。 
下面将详细的阐述如何发送Broadcast和使用BroadcastReceiver过滤接收的过程： 
首先在需要发送信息的地方，把要发送的信息和用于过滤的信息(如Action、Category)装入一个Intent对象，然后通过调用 sendOrderBroadcast()或sendStickyBroadcast()方法，把 Intent对象以广播方式发送出去。 
当Intent发送以后，所有已经注册的BroadcastReceiver会检查注册时的IntentFilter是否与发送的Intent 相匹配，若匹配则就会调用BroadcastReceiver的onReceive()方法。所以当我们定义一个BroadcastReceiver的时 候，都需要实现onReceive()方法。 
注册BroadcastReceiver有两种方式: 
1、静态注册：在AndroidManifest.xml中用标签生命注册，并在标签内用标签设置过滤器。 
```  
<receiver android:name="myRecevice">    //继承BroadcastReceiver，重写onReceiver方法 
<intent-filter>     
	<action android:name="com.dragon.net"></action> //使用过滤器，接收指定action广播
</intent-filter> 
</receiver> 
```
2、动态注册： 
```  
IntentFilter intentFilter = new IntentFilter();
intentFilter.addAction(String);   //为BroadcastReceiver指定action，使之用于接收同action的广播
registerReceiver(BroadcastReceiver,intentFilter);
```
一般：在onStart中注册，onStop中取消unregisterReceiver 
指定广播目标Action：Intent intent = new Intent(actionString); 
并且可通过Intent携带消息 :intent.putExtra("msg", "hi,我通过广播发送消息了"); 
发送广播消息：Context.sendBroadcast(intent ) 
其中在动态注册中可将BroadcastReceiver的继承类进行封装，添加构造函数和BroadcastReceiver注册 
代码： 
```  
import android.app.Notification;
import android.app.NotificationManager;
import android.app.PendingIntent;
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.content.IntentFilter;
public class BroadcastReceiverHelper extends BroadcastReceiver {
	NotificationManager mn = null;
	Notification notification = null;
	Context ct = null;
	BroadcastReceiverHelper receiver;
	public BroadcastReceiverHelper(Context c) {
		ct = c;
		receiver = this;
	}
	// 注册
	public void registerAction(String action) {
		IntentFilter filter = new IntentFilter();
		filter.addAction(action);
		ct.registerReceiver(receiver, filter);
	}
	@Override
	public void onReceive(Context context, Intent intent) {
		String msg = intent.getStringExtra("msg");
		int id = intent.getIntExtra("who", 0);
		if (intent.getAction().equals("com.cbin.sendMsg")) {
			mn = (NotificationManager) context
					.getSystemService(Context.NOTIFICATION_SERVICE);
			notification = new Notification(R.drawable.icon, id + "发送广播",
					System.currentTimeMillis());
			Intent it = new Intent(context, Main.class);
			PendingIntent contentIntent = PendingIntent.getActivity(context, 0,
					it, 0);
			notification.setLatestEventInfo(context, "msg", msg, contentIntent);
			mn.notify(0, notification);
		}
	}
}
```
然后再Activity中声明BroadcastReceiver的扩展对象，在onStart中注册，onStop中卸载。 
```  
BroadcastReceiverHelper rhelper;
@Override
public void onStart() {
	// 注册广播接收器
	rhelper = new BroadcastReceiverHelper(this);
	rhelper.registerAction("com.cbin.sendMsg");
	super.onStart();
}
@Override
public void onStop() {
	// 取消广播接收器
	unregisterReceiver(rhelper);
	super.onStop();
}
```