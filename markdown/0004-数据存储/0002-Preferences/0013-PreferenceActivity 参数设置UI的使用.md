Android为了方便管理SharedPreferences，为我们提供了一个很简洁高效的PreferenceActivity。通过继承PreferenceActivity这个类，我们很轻松的就能实现一个程序参数设置的UI界面。
具体步骤如下：
1.添加Preference的布局，在 /res/xml/目录下添加一个settings.xml文件，内容如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<referenceScreen xmlns:android="http://schemas.android.com/apk/res/android" 
        android:title="Settings">
        <CheckBoxPreference android:title="android with google" 
                android:key="android"></CheckBoxPreference>
        <referenceCategory android:title="eoe">
                <ListPreference android:title="eoeList" 
                        android:summary="Set eoe Options" android:key="eoe" 
                        android:dialogTitle="Choose eoe Options" android:entries="@array/androidBook" 
                        android:entryValues="@array/androidBook"></ListPreference>
        </PreferenceCategory>
</PreferenceScreen>
```
2. 生成一个SettingsActivity继承自PreferenceActivity。
```  
public class SettingsActivity extends PreferenceActivity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		addPreferencesFromResource(R.xml.settings);
	}
}
```
3.添加R.xml.settings布局文件
addPreferencesFromResource(R.xml.settings)
4.当程序运行后，会生成/data/data/[PACKAGE_NAME]/shared_prefs/[PACKAGE_NAME]_preferences.xml 参数配置文件。
com.eoeandroid.book_preferences.xml
```  
<?xml version='1.0' encoding='utf-8' standalone='yes' ?>
<map>
	<boolean name="android" value="false" />
	<string name="eoe">eoemarket</string>
</map>
```
5.获得SharedPreferences引用
```  
SharedPreferences sp = getPreferenceManager().getDefaultSharedPreferences(this);
```

![img](http://emanual.github.io/md-android/img/data_preference/14_preference.jpg) 
![img](http://emanual.github.io/md-android/img/data_preference/14_preference2.jpg) 