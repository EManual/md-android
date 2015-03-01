SharedUserId
#### 关于SharedUserId的总结：
我们知道一般每个app都有一个唯一的linux user ID，则这样权限就被设置成该应用程序的文件只对该用户可见，只对该应用程序自身可见，而我们可以使他们对其他的应用程序可见，这会使我们用到SharedUserId，也就是让两个apk使用相同的userID，这样它们就可以看到对方的文件。为了节省资源，具有相同ID的apk也可以在相同的linux进程中进行(这儿需要注意，并不是一定要在一个进程里面运行)，共享一个虚拟机。
![img](P)  
我们可以建立两个application，分别为test_a和test_b,我们的目的就是让test_b访问test_a里面的文件或者是数据：具体做法如下:
在test_a应用程序的包com.test1的manifest里面添加anroid：shareuserid=“com.test2”（注：这儿test_a是被访的apk，so 加上这句  android:exported="false"说明它是私有的，然后会让shareuserid应用更有力）。
具体内容如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.test1"
    android:exported="false
    android:sharedUserId="com.test2"
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".TestAcitvity1"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```
然后再应用程序test_b的包com.test2下的manifest.xml中添加anroid： 
```  
shareuserid="com.test2"
```
然后在test_b的TestActivity2中添加如下代码：
```  
private Button.OnClickListener button_listener = new Button.OnClickListener() {
	public void onClick(View v) {
		Intent intent = new Intent();
		intent.setClassName("us.imnet.iceskysl.db",
				"us.imnet.iceskysl.db.DBSharedPreferences");
		// intent.setClassName("com.test1","com.test1.TestActivity1");
		startActivity(intent);
	}
};
``` 
这样就可以调整跳转，然后就可以运行。完整代码是：
```   
import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
public class TestActivity2 extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		find_and_modify_button();
	}
	private void find_and_modify_button() {
		Button change_test1 = (Button) findViewById(R.id.change_test1);
		change_test1.setOnClickListener(button_listener);
	}
	private Button.OnClickListener button_listener = new Button.OnClickListener() {
		public void onClick(View v) {
			Intent intent = new Intent();
			intent.setClassName("us.imnet.iceskysl.db",
					"us.imnet.iceskysl.db.DBSharedPreferences");
			// intent.setClassName("com.test1","com.test1.TestActivity1");
			startActivity(intent);
		}
	};
}
```
运行结果是：从test_b的TestActivity1中进去，然后跳到TestActivity2中，这样可以读取到它里面的数据出来。
文件访问：
可以通过getSharedPreferences(String,int),openFileOutput(String,int)或者openOrCreateDatabase(String,int,SQLiteDatabase.CursorFactory)创建一个新文件时,你可以同时或分别使用MODE_WORLD_READABLE和MODE_WORLD_WRITEABLE标志允许其它包读/写此文件。
下面是用getSharedPreferences(String, int),创建到文件，修改它的属性为MODE_WORLD_WRITEABLE 则看到它的文件权限的变化。
（补充）：关于linux下面文件权限
第2～10个字符当中的每3个为一组，左边三个字符表示所有者权限，中间3个字符表示与所有者同一组的用户的权限，右边3个字符是其他用户的权限。这三个一组共9个字符，代表的意义如下：   
r(Read，读取)：对文件而言，具有读取文件内容的权限；对目录来说，具有浏览目 录的权限。   
w(Write,写入)：对文件而言，具有新增、修改文件内容的权限；对目录来说，具有删除、移动目录内文件的权限。   
x(eXecute，执行)：对文件而言，具有执行文件的权限；对目录了来说该用户具有进入目录的权限。   
－：表示不具有该项权限。
![img](P)  