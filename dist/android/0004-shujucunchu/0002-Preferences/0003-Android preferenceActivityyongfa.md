在开发应用程序的过程中我们有很大的机会需要用到参数设置功能，那么在Android应用中，我们如何实现参数设置界面及参数存储呢，下面我们来介绍一下Android中的一个特殊Activity?PreferencesActivity。PreferencesActivity是Android中专门用来实现程序设置界面及参数存储的一个Activity，我们用一个实例来简介如何使用PreferencesActivity。
以此为例我们来介绍一下如何实现这个界面。首先建立一个xml来描述这个界面，文件为res/xml/preferences.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android" >
    <PreferenceCategory android:title="PreferenceCategory 1" >
        <CheckBoxPreference
            android:defaultValue="true"
            android:key="CheckBox1"
            android:summaryOff="某功能: 关闭"
            android:summaryOn="某功能: 开启"
            android:title="CheckBox" />
    </PreferenceCategory>
    <PreferenceCategory android:title="PreferenceCategory 2" >
        <PreferenceScreen android:title="二级PreferenceScreen" >
            <CheckBoxPreference
                android:defaultValue="true"
                android:key="CheckBox2"
                android:summaryOff="某功能: 关闭"
                android:summaryOn="某功能: 开启"
                android:title="CheckBox" />
        </PreferenceScreen>
    </PreferenceCategory>
    <PreferenceCategory android:title="PreferenceCategory 3" >
        <ListPreference
            android:dialogTitle="ListPreference"
            android:entries="@array/entries_list_preference"
            android:entryValues="@array/entriesvalue_list_preference"
            android:key="ListPreference"
            android:summary="ListPreference测试"
            android:title="ListPreference" />
        <EditTextPreference
            android:dialogTitle="输入设置"
            android:key="EditTextPreference"
            android:summary="点击输入"
            android:title="EditTextPreference" />
        <RingtonePreference
            android:key="RingtonePreference"
            android:summary="选择铃声"
            android:title="RingtonePreference" />
    </PreferenceCategory>
</PreferenceScreen>
```
这个例子中包括了PreferenceActivity中常见的几种组件，以下为具体介绍及用法：
PreferenceScreen：设置页面，可嵌套形成二级设置页面，用Title参数设置标题。
PreferenceCategory：某一类相关的设置，可用Title参数设置标题。
CheckBoxPreference：是一个CheckBox设置，只有两种值，true或false，可用Title参数设置标题，用summaryOn和summaryOff参数来设置控件选中和未选中时的提示。
ListPreference：下拉框选择控件，用Title参数设置标题，用Summary参数设置说明，点击后出现下拉框，用dialogTitle设置下拉框的标题，下拉框内显示的内容和具体的值需要在res/values/array.xml中设置两个array来表示。图中的array.xml设置如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
<string-array name="entries_list_preference">
	<item>test1</item>
	<item>test2</item>
	<item>test3</item>
</string-array>
<string-array name="entriesvalue_list_preference">
	<item>1</item>
	<item>2</item>
	<item>3</item>
</string-array>
</resources>
```
EditTextPreference：输入框控件，点击后可输入字符串设置。用Title参数设置标题，Summary参数设置说明，dialogTitle参数设置输入框的标题。
RingtonePreference：铃声选择框，点击后可选择系统铃声。Title参数设置标题，Summary参数设置说明，dialogTitle参数设置铃声选择框的标题。
以上是PreferenceActivity的xml描述，那么在程序中我们只需要新建一个继承自PreferenceActivity的Activity，然后在主程序中调用就可以了。这个PreferenceActivity中的设置存储是完全自动的，你不需要再用代码去实现设置的存储，PreferenceActivity创建后会自动创建一个配置文件/data/data/you_package_name /shared_prefs/you_package_name_you_xml_name.xml。上例中自动生成的配置文件如下：
```  
<?xml version='1.0' encoding='utf-8' standalone='yes' ?>
<map>
	<string name="EditTextPreference">12332312</string>
	<string name="ListPreference">2</string>
	<string name="RingtonePreference">content://settings/system/ringtone</string>
	<boolean name="CheckBox1" value="true" />
	<boolean name="CheckBox2" value="true" />
</map>
```