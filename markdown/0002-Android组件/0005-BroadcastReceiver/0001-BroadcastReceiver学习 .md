在实际应用中，我们常需要等，等待系统抑或其他应用发出一道指令，为自己的应用擦亮明灯指明方向。而这种等待，在很多的平台上，都会需要付出不小的代价。
比如，在Symbian中，你要等待一个来电消息，显示归属地之类的，必须让自己的应用忍辱负重偷偷摸摸的开机启动，消隐图标隐藏任务项，潜伏在后台，监控着相关事件，等待转瞬即逝的出手机会。这是一件很发指的事情，不但白白耗费了系统资源，还留了个流氓软件的骂名，这真是卖力不讨好的正面典型。
在Android中，充分考虑了广泛的这类需求，于是就有了Broadcast Receiver这样的一个组件。每个Broadcast Receiver都可以接收一种或若干种Intent作为触发事件（有不知道Intent的么，后面会知道了...），当发生这样事件的时候，系统会负责唤醒或传递消息到该Broadcast Receiver，任其处置。在此之前和这以后，Broadcast Receiver是否在运行都变得不重要了，及其绿色环保。
这个实现机制，显然是基于一种注册方式的，Broadcast Receiver将其特征描述并注册在系统中，根据注册时机，可以分为两类，被我冠名为冷热插拔。所谓冷插拔，就是Broadcast Receiver的相关信息写在配置文件中（求配置文件详情？稍安，后续奉上...），系统会负责在相关事件发生的时候及时通知到该Broadcast Receiver，这种模式适合于这样的场景。某事件方式->通知Broadcast->启动相关处理应用。比如，监听来电、邮件、短信之类的，都隶属于这种模式。而热插拔，顾名思义，插拔这样的事情，都是由应用自己来处理的，通常是在OnResume事件中通过registerReceiver进行注册，在OnPause等事件中反注册，通过这种方式使其能够在运行期间保持对相关事件的关注。比如，一款优秀的词典软件（比如，有道词典...），可能会有在运行期间关注网络状况变化的需求，使其可以在有廉价网络的时候优先使用网络查询词汇，在其他情况下，首先通过本地词库来查词，从而兼顾腰包和体验，一举两得一石二鸟一箭双雕（注，真实在有道词典中有这样的能力，但不是通过 Broadcast Receiver实现的，仅以为例...）。而这样的监听，只需要在其工作状态下保持就好，不运行的时候，管你是天大的网路变化，与我何干。其模式可以归结为：启动应用 -> 监听事件 -> 发生时进行处理。
除了接受消息的一方有多种模式，发送者也有很重要的选择权。通常，发送这有两类，一个就是系统本身，我们称之为系统Broadcast消息，在reference/android/content/Intent.html的Standard Broadcast Actions，可以求到相关消息的详情。除了系统，自定义的应用可以放出Broadcast消息，通过的接口可以是Context.sendBroadcast，抑或是Context.sendOrderedBroadcast。前者发出的称为Normal broadcast，所有关注该消息的Receiver，都有机会获得并进行处理；后者放出的称作Ordered broadcasts，顾名思义，接受者需要按资排辈，排在后面的只能吃前面吃剩下的，前面的心情不好私吞了，后面的只能喝西北风了。
当Broadcast Receiver接收到相关的消息，它们通常做一些简单的处理，然后转化称为一条Notification，一次振铃，一次震动，抑或是启动一个 Activity进行进一步的交互和处理。所以，虽然Broadcast整个逻辑不复杂，却是足够有用和好用，它统一了Android的事件广播模型，让很多平台都相形见绌了。
```  
import android.R;
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.widget.TextView;
public class EX06_05 extends Activity {
	/* 声明一个TextView,String数组与两个文本字符串变量 */
	private TextView mTextView1;
	public String[] strEmailReciver;
	public String strEmailSubject;
	public String strEmailBody;
	[Tags]/** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		/* 通过findViewById构造器创建TextView对象 */
		mTextView1 = (TextView) findViewById(R.id.myTextView1);
		mTextView1.setText("等待接收短信...");
		try {
			/* 取得短信传来的bundle */
			Bundle bunde = this.getIntent().getExtras();
			if (bunde != null) {
				/* 将bunde内的字符串取出 */
				String sb = bunde.getString("STR_INPUT");
				/* 自定义一Intent来运行寄送E-mail的工作 */
				Intent mEmailIntent = new Intent(
						android.content.Intent.ACTION_SEND);
				/* 设置邮件格式为"plain/text" */
				mEmailIntent.setType("plain/text");
				/*
				 [Tags]* 取得EditText01,02,03,04的值作为 收件人地址,附件,主题,正文
				 [Tags]*/
				strEmailReciver = new String[] { "[url=mailto:jay.mingchieh@gmail.com]jay.mingchieh@gmail.com[/url" };
				strEmailSubject = "你有一封短信!!";
				strEmailBody = sb.toString();
				/* 将取得的字符串放入mEmailIntent中 */
				mEmailIntent.putExtra(android.content.Intent.EXTRA_EMAIL,
						strEmailReciver);
				mEmailIntent.putExtra(android.content.Intent.EXTRA_SUBJECT,
						strEmailSubject);
				mEmailIntent.putExtra(android.content.Intent.EXTRA_TEXT,
						strEmailBody);
				startActivity(Intent.createChooser(mEmailIntent, getResources()
						.getString(R.string.str_message)));
			} else {
				finish();
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
/*引用BroadcastReceiver类*/
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.os.Bundle;
import android.telephony.gsm.SmsMessage;
import android.widget.Toast;
/*自定义继承自BroadcastReceiver类,聆听系统服务广播的信息 */
public class EX06_05SMSreceiver extends BroadcastReceiver {
	/*
	 [Tags]* 声明静态字符串,并使用 android.provider.Telephony.SMS_RECEIVED 作为Action为短信的依据
	 [Tags]*/
	private static final String mACTION = "android.provider.Telephony.SMS_RECEIVED";
	private String str_receive = "收到短信!";
	@Override
	public void onReceive(Context context, Intent intent) {
		// TODO Auto-generated method stub
		Toast.makeText(context, str_receive.toString(), Toast.LENGTH_LONG)
				.show();
		/* 判断传来Intent是否为短信 */
		if (intent.getAction().equals(mACTION)) {
			/* 建构一字符串集合变量sb */
			StringBuilder sb = new StringBuilder();
			/* 接收由Intent传来的数据 */
			Bundle bundle = intent.getExtras();
			/* 判断Intent是有数据 */
			if (bundle != null) {
				/*
				 [Tags]* pdus为 android内置短信参数 identifier 通过bundle.get("")返回一包含pdus的对象
				 [Tags]*/
				Object[] myOBJpdus = (Object[]) bundle.get("pdus");
				/* 构造短信对象array,并依据收到的对象长度来创建array的大小 */
				SmsMessage[] messages = new SmsMessage[myOBJpdus.length];
				for (int i = 0; i < myOBJpdus.length; i++) {
					messages[i] = SmsMessage
							.createFromPdu((byte[]) myOBJpdus[i]);
				}
				/* 将送来的短信合并自定义信息于StringBuilder当中 */
				for (SmsMessage currentMessage : messages) {
					sb.append("接收到来自:\n");
					/* 收信人的电话号码 */
					sb.append(currentMessage.getDisplayOriginatingAddress());
					sb.append("\n------传来的短信------\n");
					/* 取得传来信息的BODY */
					sb.append(currentMessage.getDisplayMessageBody());
					Toast.makeText(context, sb.toString(), Toast.LENGTH_LONG)
							.show();
				}
			}
			/* 以Notification(Toase)显示来讯信息 */
			Toast.makeText(context, sb.toString(), Toast.LENGTH_LONG).show();
			/* 返回主Activity */
			Intent i = new Intent(context, EX06_05.class);
			/* 自定义一Bundle */
			Bundle mbundle = new Bundle();
			/* 将短信信息以putString()方法存入自定义的bundle内 */
			mbundle.putString("STR_INPUT", sb.toString());
			/* 将自定义bundle写入Intent中 */
			i.putExtras(mbundle);
			/* 设置Intent的Flag以一个全新的task来运行 */
			i.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
			context.startActivity(i);
		}
	}
}
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android=<a href="http://schemas.android.com/apk/res/android">http://schemas.android.com/apk/res/android</a>
	package="irdc.EX06_05
	android:versionCode="1
	android:versionName="1.0.0">
    <application android:icon="@drawable/icon" android:label="@string/app_name">
    <activity android:name=".EX06_05
      android:label="@string/app_name">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
    </activity>
   <!-- 建立receiver聆聽系統廣播訊息 -->
    <receiver android:name="EX06_05SMSreceiver">
    <!-- 設定要捕捉的訊息名稱為provider中Telephony.SMS_RECEIVED -->
<intent-filter> 
    <action
      android:name="android.provider.Telephony.SMS_RECEIVED" />
</intent-filter> 
</receiver> 
    </application>
<uses-permission android:name="android.permission.RECEIVE_SMS"></uses-permission>
</manifest>
```