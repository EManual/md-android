#### Using intent matching 使用intent匹配
intent和intent filter相匹配，不仅为了寻找并启动一个目标组件，也是为了寻找设备上组件的信息。例如，android系统启动了应用程序启动器，该程序位于屏幕的顶层，显示了用户可以启动的程序，这是通过查找设备上所有的action为"android.intent.action.MAIN"，category为"android.intent.category.LAUNCHER"的intent filter所在的activity实现的。然后它显示了这些activity的图标和标题。类似的，它通过寻找 "android.intent.category.HOME"的filter来定位主屏幕程序。
应用程序可以用相同的方式来使用intent匹配。PackageManager有一组query...()方法来寻找接受某个特定intent的所有组件，还有一系列resolve...()方法来决定响应一个intent的最佳组件。例如，queryIntentActivities()返回一个activity列表，这些activity可以执行传入的intent。类似的还有queryIntentServices()和queryIntentBroadcastReceivers()。
#### Note Pad Example例子:记事本
记事本示例程序让用户可以浏览一个笔记列表，查看，编辑，删除和增加笔记。这一节关注该程序定义的intent filter。
在其manifest文件中，记事本程序定义了三个activity, 每个有至少一个intent filter。它还定义了一个content provider来管理笔记数据。
manifest 文件如下:
```  
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="eoe.demo">
<application android:icon="@drawable/app_notes" 
	android:label="@string/app_name" >
<provider android:name="NotePadProvider" 
	android:authorities="com.google.provider.NotePad" />
<activity android:name="NotesList" android:label="@string/title_notes_list">
	<intent-filter>
		<action android:name="android.intent.action.MAIN" />
		<category android:name="android.intent.category.LAUNCHER" />
	</intent-filter>
	<intent-filter>
		<action android:name="android.intent.action.VIEW" />
		<action android:name="android.intent.action.EDIT" />
		<action android:name="android.intent.action.PICK" />
		<category android:name="android.intent.category.DEFAULT" />
		<data android:mimeType="vnd.android.cursor.dir/vnd.google.note" />
	</intent-filter>
	<intent-filter>
		<action android:name="android.intent.action.GET_CONTENT" />
		<category android:name="android.intent.category.DEFAULT" />
		<data android:mimeType="vnd.android.cursor.item/vnd.google.note" />
	</intent-filter>
</activity>
<activity android:name="NoteEditor" 
	android:theme="@android:style/Theme.Light" 
	android:label="@string/title_note" >
	<intent-filter android:label="@string/resolve_edit">
		<action android:name="android.intent.action.VIEW" />
		<action android:name="android.intent.action.EDIT" />
		<action android:name="com.android.notepad.action.EDIT_NOTE" />
		<category android:name="android.intent.category.DEFAULT" />
		<data android:mimeType="vnd.android.cursor.item/vnd.google.note" />
	</intent-filter>
	<intent-filter>
		<action android:name="android.intent.action.INSERT" />
		<category android:name="android.intent.category.DEFAULT" />
		<data android:mimeType="vnd.android.cursor.dir/vnd.google.note" />
	</intent-filter>
</activity>
<activity android:name="TitleEditor" 
	android:label="@string/title_edit_title" 
	android:theme="@android:style/Theme.Dialog">
	<intent-filter android:label="@string/resolve_title">
		<action android:name="com.android.notepad.action.EDIT_TITLE" />
		<category android:name="android.intent.category.DEFAULT" />
		<category android:name="android.intent.category.ALTERNATIVE" />
		<category android:name="android.intent.category.SELECTED_ALTERNATIVE" />
		<data android:mimeType="vnd.android.cursor.item/vnd.google.note" />
	</intent-filter>
</activity>
</application>
</manifest>
```
第一个activity，NoteList，和其它activity不同，因为它操作一个笔记的目录(笔记列表)，而不是一个单独的笔记。它一般作为该程序的初始界面。它可以做以下三件事:
```  
<intent-filter>
	<action android:name="android.intent.action.MAIN" />
	<category android:name="android.intent.category.LAUNCHER" />
</intent-filter>
```
该filter声明了记事本应用程序的主入口。标准的MAIN action是一个不需要任何其它信息(例如数据等)的程序入口，LAUNCHER category表示该入口应该在应用程序启动器中列出。
```  
<intent-filter>
	<action android:name="android.intent.action.VIEW" />
	<action android:name="android.intent.action.EDIT" />
	<action android:name="android.intent.action.PICK" />
	<category android:name="android.intent.category.DEFAULT" />
	<data android:mimeType="vnd.android.cursor.dir/vnd.google.note" />
</intent-filter> 
```