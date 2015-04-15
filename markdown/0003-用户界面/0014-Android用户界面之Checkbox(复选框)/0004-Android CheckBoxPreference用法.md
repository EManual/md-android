我们介绍了下ListPreference的用法。这里我们再介绍下其他几个首选项的用法：
![img](http://emanual.github.io/md-android/img/view_checkbox/05_checkbox.jpg) 
activity中：
```  
import android.os.Bundle;
import android.preference.PreferenceActivity;
 /**
  * @description 有关首选项preferences的研究
  */
public class MyPreferencesActivity extends PreferenceActivity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		addPreferencesFromResource(R.xml.checkbox);
	}
}
```
res/xml/checkbox.xml布局文件
```  
<?xml version="1.0" encoding="utf-8"?>
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android"
    android:key="mycheckbox_screen"
    android:summary="复选框介绍"
    android:title="屏幕标题" >
    <CheckBoxPreference
        android:key="shandong"
        android:summaryOff="山东未被选中"
        android:summaryOn="山东被选中了"
        android:title="山东" >
    </CheckBoxPreference>
    <CheckBoxPreference
        android:key="shanghai"
        android:summaryOff="上海未被选中"
        android:summaryOn="上海被选中了"
        android:title="上海" >
    </CheckBoxPreference>
    <CheckBoxPreference
        android:key="yunnan"
        android:summaryOff="云南未被选中"
        android:summaryOn="云南被选中了"
        android:title="云南" >
    </CheckBoxPreference>
</PreferenceScreen>
```
用法和ListPreference相比，简单多了，这里就不多介绍了，这里我给大家看看后台的xml文件：
cn.com.chenzheng_java.pref_preferences.xml
大家首先注意下，android是怎么给我们命名的，我们的包名cn.com.chenzheng_java加上.pref_preferences哦，有些时候，如果我们不通过继承PreferenceActivity，而是通过activity中的getSharedPreferences方法进行操作时，我们会用到该文件的名称的哦。
文件内容：
```  
<?xml version='1.0' encoding='utf-8' standalone='yes' ?>
<map>
	<string name="myListPreference">hebei1</string>
	<boolean name="shanghai" value="true" />
	<boolean name="shandong" value="true" />
</map> 
```