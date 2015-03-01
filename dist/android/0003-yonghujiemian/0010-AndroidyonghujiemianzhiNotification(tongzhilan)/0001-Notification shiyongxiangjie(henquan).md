当用户有没有接到的电话的时候，Android顶部状态栏里就会出现一个小图标。提示用户有没有处理的快讯，当拖动状态栏时，可以查看这些快讯。Android给我们提供了NotificationManager来管理这个状态栏。可以很轻松的完成。
如果要添加一个Notification,可以按照以下几个步骤
1：获取NotificationManager:
```  
NotificationManager m_NotificationManager = (NotificationManager)this.getSystemService(NOTIFICATION_SERVICE);
```
2:定义一个Notification:
```  
Notification  m_Notification = new Notification();
```
3:设置Notification的各种属性：
```  
//设置通知在状态栏显示的图标
m_Notification.icon=R.drawable.icon;
//当我们点击通知时显示的内容
m_Notification.tickerText="Button1 通知内容.....";
通知时发出的默认声音
m_Notification.defaults=Notification.DEFAULT_SOUND;
//设置通知显示的参数
Intentm_Intent=new Intent(NotificationDemo.this,DesActivity.class); 
PendingIntent m_PendingIntent=PendingIntent.getActivity(NotificationDemo.this, 0, m_Intent, 0);
m_Notification.setLatestEventInfo(NotificationDemo.this, "Button1", "Button1通知",m_PendingIntent );
//这个可以理解为开始执行这个通知
m_NotificationManager.notify(0,m_Notification);
```
4：既然可以增加同样我们也可以删除。当然是只是删除你自己增加的。
```  
m_NotificationManager.cancel(0); 
```
这里的0是一个ID号码，和notify第一个参数0一样。
这也就完成了，添加删除工作。
这里我们还是一个Demo来掩饰我们的操作。
1：新建一个工程NotificationDemo。
2：添加一个Activity：DesActivity,注意需要在Manifest.xml中声明
3：NotificationDemo中的Laytout文件很简单就是定义一个Button.其代码文件如下：
```  
import android.app.Activity;
import android.app.Notification;
import android.app.NotificationManager;
import android.app.PendingIntent;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
public class NotificationDemo extends Activity {
	Button m_Button1;
	TextView m_txtView;
	NotificationManager m_NotificationManager;
	Notification m_Notification;
	Intent m_Intent;
	PendingIntent m_PendingIntent;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		m_NotificationManager = (NotificationManager) this
				.getSystemService(NOTIFICATION_SERVICE);
		m_Button1 = (Button) this.findViewById(R.id.Button01);
		// 点击通知时转移内容
		m_Intent = new Intent(NotificationDemo.this, DesActivity.class);
		m_PendingIntent = PendingIntent.getActivity(NotificationDemo.this, 0,
				m_Intent, 0);
		m_Notification = new Notification();
		m_Button1.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View v) {
				// 设置通知在状态栏显示的图标
				m_Notification.icon = R.drawable.icon;
				// 当我们点击通知时显示的内容
				m_Notification.tickerText = "Button1 通知内容.....";
				// 通知时发出的默认声音
				m_Notification.defaults = Notification.DEFAULT_SOUND;
				// 设置通知显示的参数
				m_Notification.setLatestEventInfo(NotificationDemo.this,
						"Button1", "Button1通知", m_PendingIntent);
				// 这个可以理解为开始执行这个通知
				m_NotificationManager.notify(0, m_Notification);
			}
		});
	}
}
```
4：修改DesActivity 的源文件，代码如下：它做的事情就是取消之前添加的Notification
```  
import android.app.Activity;
import android.app.NotificationManager;
import android.os.Bundle;
public class DesActivity extends Activity {
	NotificationManager m_NotificationManager;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		this.setContentView(R.layout.main2);
		// 启动后删除之前我们定义的
		m_NotificationManager = (NotificationManager) this
				.getSystemService(NOTIFICATION_SERVICE);
		m_NotificationManager.cancel(0);
	}
}
```
创建和触发Notification 
创建和配置新的Notification需要经历三步。 
首先，你要创建一个新的Notification对象，传入要在状态条上显示的图 标、文字和Notification的 当前时间，如下面的代码片段所示： 
```  
// Choose a drawable to display as the status bar icon 
int icon = R.drawable.icon; 
// Text to display in the status bar when the notification is launched 
String tickerText = “Notification”; 
// The extended status bar orders notification in time order 
long when = System.currentTimeMillis(); 
Notification notification = new Notification(icon, tickerText, when); 
```
当Notification触发时，文本将沿着状态条进行滚动 显示。 
其次，使用setLatestEventInfo方法来配置Notification在扩展的状态窗口中的外观。扩展的状态窗口将显示图标和在构造函数中传入的时间，以及显示标题和一个详细的字符串。Notification一般表示为对一个动作的请求或引起用户的注意，所以，当用户点击Notification项目时你可以指定一个PendingIntent来触发。 
下面的代码片段使用了setLastestEventInfo来设置这些值： 
```  
Context context = getApplicationContext(); 
// Text to display in the extended status window 
String expandedText = “Extended status text”; 
// Title for the expanded status 
String expandedTitle = “Notification Title”; 
// Intent to launch an activity when the extended text is clicked 
Intent intent = new Intent(this, MyActivity.class); 
PendingIntent launchIntent = PendingIntent.getActivity(context, 0, intent, 0); 
notification.setLatestEventInfo(context, expandedTitle, expandedText, launchIntent); 
```
一个好的形式是显示相同事件（例如，接收多个SMS消息）的多个对象时使用一个Notification图标。为了呈现给用户，使用setLastestEventInfo更新数据集来呈现最近的消息以及重新触发Notification来更新它的值。 
你还可以使用number属性来显示一个状态条图标所表示的事件数目。 
设置为比1大的数，如下所示，将在状态条图标上以一个小小的数字进行 显示： 
```  
notification.number++; 
```
任何对Notification的变更，你都需要重新触发来进行更 新。如果要删除这个数字，设置number的值为0或者-1。 
最后，你可以对Notification对象使用多种属性来增强Notification的效果，如闪烁LED、震动电话和播放音乐文件。这些高级特征将在本章的后面部分进行详细描述。 
触发Notification 
为了触发一个Notification，使用NotificationManager的notify方法，将一个整数的ID和Notification对象传入，如下的片段所示： 
```  
int notificationRef = 1; 
notificationManager.notify(notificationRef, notification); 
``` 
为了更新一个已经触发过的Notification，传入相同的ID。你既可以传入相同的Notification对象，也可以是一个全新的对象。只要ID相同，新的Notification对象会替换状态条 
图标和扩展的状态窗口的细节。 
你还可以使用ID来取消Notification，通过调用NotificationManager的cancel方法，如下所示： 
```  
notificationManager.cancel(notificationRef); 
```
取消一个Notification时，将移除它的状态条图标以及清除 在扩展的状态窗口中的信息。 
Notification和NotificationManager的基本使用方法 
1. NotificationManager和Notification用来设置通知。 
通知的设置等操作相对比较简单，基本的使用方式就是用新建一个Notification对象，然后设置好通知的各项参数，然后使用系统后台运行的 NotificationManager服务将通知发出来。 
基本步骤如下： 
1）得到NotificationManager： 
```  
String ns = Context.NOTIFICATION_SERVICE; 
NotificationManager mNotificationManager = (NotificationManager) getSystemService(ns); 
```
2）创建一个新的Notification对象： 
```  
Notification notification = new Notification(); 
notification.icon = R.drawable.notification_icon; 
```
也可以使用稍微复杂一些的方式创建Notification： 
```  
int icon = R.drawable.notification_icon; //通知图标 
CharSequence tickerText = "Hello";  //状态栏(Status Bar)显示的通知文本提示 
long when = System.currentTimeMillis(); //通知产生的时间，会在通知信息里显示 
Notification notification = new Notification(icon, tickerText, when); 
```
3）填充Notification的各个属性： 
```  
Context context = getApplicationContext(); 
CharSequence contentTitle = "My notification"; 
CharSequence contentText = "Hello World!"; 
Intent notificationIntent = new Intent(this, MyClass.class); 
PendingIntent contentIntent = PendingIntent.getActivity(this, 0, notificationIntent, 0); 
notification.setLatestEventInfo(context, contentTitle, contentText, contentIntent); 
```
Notification提供了丰富的手机提示方式： 
a)在状态栏(Status Bar)显示的通知文本提示，如： 
```  
notification.tickerText = "hello"; 
```
b)发出提示音，如： 
```  
notification.defaults |= Notification.DEFAULT_SOUND; 
notification.sound = Uri.parse("file:///sdcard/notification/ringer.mp3"); 
notification.sound = Uri.withAppendedPath(Audio.Media.INTERNAL_CONTENT_URI, "6");
```
c)手机振动，如： 
```  
notification.defaults |= Notification.DEFAULT_VIBRATE; 
long[] vibrate = {0,100,200,300}; 
notification.vibrate = vibrate; 
```
d)LED灯闪烁，如： 
```  
notification.defaults |= Notification.DEFAULT_LIGHTS; 
notification.ledARGB = 0xff00ff00; 
notification.ledOnMS = 300; 
notification.ledOffMS = 1000; 
notification.flags |= Notification.FLAG_SHOW_LIGHTS; 
```
4）发送通知： 
```  
private static final int ID_NOTIFICATION = 1; 
mNotificationManager.notify(ID_NOTIFICATION, notification); 
```
2. 通知的更新 
如果需要更新一个通知，只需要在设置好notification之后，再调用setLatestEventInfo，然后重新发送一次通知即可。