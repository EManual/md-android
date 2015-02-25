显然，必须为用户提供UI来查看排序选项列表。该列表将包含每个选项的单选按钮，而且默认(或当前)选项应该被预先选中。要使用Android首选项框架解决此问题，所做的工作非常之少。首先，创建首选项XML文件来描述首选项，然后使用预先构建的活动类，该类知道如何显示和持久化首选项，下面是我们的首选项XML文件 flightoptions.xml。
```  
<?xml version="1.0" encoding="utf-8"?>
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android"
    android:key="flight_option_preference"
    android:summary="@string/prefSummary"
    android:title="@string/prefTitle" >
    <ListPreference
        android:defaultValue="@string/flight_sort_option_default_value"
        android:dialogTitle="@string/dialogTitle"
        android:entries="@array/flight_sort_options"
        android:entryValues="@array/flight_sort_options_values"
        android:key="@string/selected_flight_sort_option"
        android:summary="@string/listSummary"
        android:title="@string/listTitle" />

</PreferenceScreen>
```
上边说了我们还需要一个Activity类FlightPreferenceActivity，下面使我们的Activity类。这个Activity类继承了PreferenceActivity 代码如下：
```  
import android.os.Bundle;
import android.preference.PreferenceActivity;
public class FlightPreferenceActivity extends PreferenceActivity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		addPreferencesFromResource(R.xml.flightoptions);
	}
}
```
上面的代码清单，包含了一个表示航班选项示例的首选项设置的XML片段。该包含了一个活动类，也就是我们的FlightPreferenceActivity这个类主要用于加载我们的XML文件。首先看一下XML。Android提供了一种端到端得首选项框架。这意味着，该框架支持定义首选项，想用户显示设置，以及将用户选择持久化到数据库存储中。在/res/xml/目录下的XML文件中定义首选项。要向用户显示首选项，编写一个活动类来扩展预定义的Android类android.preference.PreferenceActivity，然后使用 addPreferencesFromResource()方法将资源添加到活动的资源集合中。