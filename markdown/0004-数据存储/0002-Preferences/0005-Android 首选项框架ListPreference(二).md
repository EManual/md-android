在本航班场景中，在/res/xml/目录下创建文件flightoptions.xml。然后创建活动类FlightPreferenceActivity，它扩展了android.preference.PreferenceActivity类。接下来调用addPreferencesFromResource()传入R.xml.flightoptions。请注意，首选项资源XML指向多个字符串资源。为了确保正确编译，需要向项目中添加多个字符串资源。现在先看一下，上面得代码清单会生成什么样子的UI。

![img](http://emanual.github.io/md-android/img/data_preference/06_preference.jpg) 
![img](http://emanual.github.io/md-android/img/data_preference/06_preference2.jpg) 

上边有两个视图。左边的视图称为首选项屏幕，右边的UI是一个列表首选项。当用户选择 Flight Options时， Choose Flight Options视图将以模态对话框的形式出现，其中包含每个选项的单选按钮。用户选择一个选项之后，将立即该选项并关闭视图。当用户返回选项屏幕时，视图将反映前面保存的选择。
前面已提到，首选项XML文件和相关的活动类，从上边我们可以看到，我们在XML文件中定义了一个PreferenceScreen，然后创建ListPreference作为子屏幕。对于 PreferenceScreen,设置了3个属性： key、title和 summary。 key 是一个字符串，可用于以编程的方式表示项 (类似于使用 android:id的方式);title 是屏幕的标题 (My Preferences) ; summary是对屏幕用途的描述。对于列表首选项，设置 key、title和 summary，以及 entries、entryValues、dialogTitle和defaultValue特性。下表总结了这些特性。
特性说明
```  
android:key选项的名称或键(比如selected_flight_sort_option)
android:title选项的标题
android:summary选项的简短摘要
android:entries可将选项设置成列表项的文本
android:entryValues定义每个列表项的值。注意：每个列表项有一些文本和 一 个 值。 文本由entries定义，值由entryValues定义。
android:dialogTitle对话框的标题，在视图显示为模态对话框时使用
android:defaultValue项列表中选项的默认值
```
为了使我们的例子能够正常运行，我们还需要添加如下文件。
```  
<?xml version="1.0" encoding="utf-8"?> 
<resources> 
<string-array name="flight_sort_options"> 
	<item>Total Cost</item>
	<item> of Stops</item>
	<item>Airline</item>
</string-array> 
<string-array name="flight_sort_options_values"> 
	<item>0</item>
	<item>1</item>
	<item>2</item>
</string-array> 
</resources>
```
当然不能少了我们的strings.xml
```  
<?xml version="1.0" encoding="utf-8"?> 
<resources> 
	<string name="hello">Hello World, FlightPreferenceActivity!</string>
	<string name="app_name">Preference_Demo</string>
	<string name="prefTitle">My Preferences</string>
	<string name="prefSummary">Set Flight Option Preferences</string>
	<string name="flight_sort_option_default_value">1</string>
	<string name="dialogTitle">Choose Flight Options</string>
	<string name="listSummary">Set Search Options</string>
	<string name="listTitle">Flight Options</string>
	<string name="selected_flight_sort_option">selected_flight_sort_option</string>
	<string name="menu_prefs_title">Settings</string>
	<string name="menu_quit_title">Quit</string>
</resources>
```
