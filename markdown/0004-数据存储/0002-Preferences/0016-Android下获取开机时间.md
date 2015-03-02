找了一圈没发现能得到开机启动时间资料，于是乎突发奇想，得到了解决方案。
我的思路是：程序里注册个广播接收器，接收开机启动的广播，当程序接到该广播后，写入文件SharedPreferences,当我们程序需要用到开机时间时，再从SharedPreferences中读取信息。
废话不多说,下面上大家喜欢的源码！
AndroidManifest.xml
```  
<receiver android:name=".receiver.BootUpReceiver">   
<intent-filter>   
  <action android:name="android.intent.action.BOOT_COMPLETED" />
</intent-filter>   
</receiver>  
```
Receiver文件，记录开机时间
```  
public class BootUpReceiver extends BroadcastReceiver {
	private SharedPreferences sharedPreferences;// 配置文件
	private Editor editor;// 更改配置文件的类实例
	@Override
	public void onReceive(Context context, Intent intent) {
		if (intent.getAction().equals(Intent.ACTION_BOOT_COMPLETED)) {
			sharedPreferences = context.getSharedPreferences("这是存储文件的名字",
					Context.MODE_PRIVATE);
			editor = sharedPreferences.edit();
			editor.putLong("存储时间的key", new Date().getTime());
			editor.commit();// 别忘了提交哦
		}
	}
}
```
读取开机时间 
```  
 /**
  * Description : 获取开机的时间
  * @return String 秒数
  */
public static long getUpTime(Activity context) {
	SharedPreferences sharedPreferences = context.getSharedPreferences(
			"这是存储文件的名字", Context.MODE_PRIVATE);
	long seconds = sharedPreferences.getLong("存储时间的key",
			new Date().getTime());
	return seconds;
}
```
效果：

![img](http://emanual.github.io/md-android/img/data_preference/17_preference.jpg)
![img](http://emanual.github.io/md-android/img/data_preference/17_preference2.png) 

开机日期是写入文件的，下面的开机总共时间是根据当前时间算出来的。