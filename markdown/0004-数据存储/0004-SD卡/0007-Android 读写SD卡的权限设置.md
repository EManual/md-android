在Android中，要模拟SD卡，要首先使用adb的mksdcard命令来建立SD卡的镜像，如何建立，大家上网查一下吧，应该很容易找到，这里不说这个问题。
但是在应用程序执行起来以后，我们可以看到sdcard的执行权限很有问题.懂Linux的人都知道，这样的权限是无法在SD开中写入内容的，也就无法建立目录.Android中对sd卡的读写权限问题。
但是，我们在adb shell命令中，依然可以在sdcard中建立目录，写入文件.这倒是也是见鬼的事情。但是，如果你想把权限更改成777，命令行并不报错，再使用ls -l查看一下，权限依然没有改变过来.我们急中生智，使用su命令将自己变成root用户，在使用chmod 777 sdcard来改变权限，发现结果依然无效。
网上多有介绍，在Android的manifest.xml文档中加入下面的声名：
```  
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>
```
试验之后，大多数人发现依然无效.没有人再把问题叙述得更详细些，不知道是什么样的心态?我的一个弟兄终于找到了问题所在.
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="byd.eagle"
    android:versionCode="1"
    android:versionName="1.0" >
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            android:name=".EagleBackup"
            android:label="@string/app_name" >
            <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-sdk android:minSdkVersion="8" />
</manifest>
```
注意一下uses-permission在manifest.xml中的位置，这是解决问题的关键。
那么我们再来写个试验程序：
```  
public class TestMakePath extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		try {
			Log.v("EagleTag", ".......hahhha");
			Log.v("EagleTag", Environment.getExternalStorageState());
			File tPath = new File("/mnt/sdcard/tmp1");
			if (tPath.canRead())
				Log.v("EagleTag", "very bad");
			if (tPath.canWrite())
				Log.v("EagleTag", "very good");
			File tPathSon = new File("/mnt/sdcard/tmp1/good");

			tPathSon.mkdirs();
		} catch (Exception e) {
			Log.v("EagleTag", "file　create　error");
		}
	}
}
```