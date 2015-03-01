注册广播有几种方式，这些方式有何优缺点？请谈谈Android引入广播机制的用意。
答：首先写一个类要继承BroadcastReceiver
第一种:在清单文件中声明,添加
```  
<receive android:name=".IncomingSMSReceiver " >
	<intent-filter>
	   <action android:name="android.provider.Telephony.SMS_RECEIVED")
	<intent-filter>
<receiver>
```
第二种使用代码进行注册如:
```  
IntentFilter filter =  new IntentFilter("android.provider.Telephony.SMS_RECEIVED");
IncomingSMSReceiver receiver = new IncomgSMSReceiver();
registerReceiver(receiver.filter);
```
两种注册类型的区别是：
1)第一种不是常驻型广播，也就是说广播跟随程序的生命周期。
2)第二种是常驻型，也就是说当应用程序关闭后，如果有信息广播来，程序也会被系统调用自动运行。
