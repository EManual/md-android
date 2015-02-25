方法onActivityResult()：完成在workspace上增加shortcut，appwidge和Livefolder；
方法onSaveInstantceState()和onRestoreInstanceState()：为了防止Sensor、Land和Port布局自动切换时数据被置空，通过onSaveInstanceState方法可以保存当前窗口的状态，在即将布局切换前将当前的Activity压入历史堆栈.如果我们的Activity在后台没有因为运行内存吃紧被清理，则切换时回触发onRestoreIntanceState()。
（2）WallpaperChooser：设置墙纸。
同理我们从onCreat()作为入口来分析这个活动的主要功能。
```  
public void onCreate(Bundle icicle) { 
	super.onCreate(icicle); 
	// 设置允许改变的窗口状态，需在 setContentView 之前调用 
	requestWindowFeature(Window.FEATURE_NO_TITLE);
	// 添加墙纸资源，将资源标识符加入到动态数组中 
	findWallpapers();
	// 显示墙纸设置屏幕的UI元素，Imageview,Gallery and Button(LinearLayout) 
	setContentView(R.layout.wallpaper_chooser);
	// 图片查看功能的实现 
	mGallery = (Gallery) findViewById(R.id.gallery);
	mGallery.setAdapter(new ImageAdapter(this));
	mGallery.setOnItemSelectedListener(this);
	mGallery.setCallbackDuringFling(false);
	// Button事件监听，点击选择setWallpaper(Resid) 
	findViewById(R.id.set).setOnClickListener(this);
	mImageView = (ImageView) findViewById(R.id.wallpaper);
} 
[code]
（3）default_searchable
对于home中任意的Acitivty，使能系统缺省Search模式，这样就可以使用android系统默认的search UI。
（4）InstallShortcutReceiver：
继承自BroadcastReceiver，重写onReceier()方法，对于发送来的Broadcast（这里指Intent）进行过滤（IntentFilt）并且响应（这里是InstallShortcut()）。
这里分析下onReceive()：
```  
<!-- Enable system-default search mode for any activity in Home --> 
<!-- Intent received used to install shortcuts from other applications --> 
public void onReceive(Context context, Intent data) { 
	//接受并过滤Intent 
	if (!ACTION_INSTALL_SHORTCUT.equals(data.getAction())) { 
		return; 
	} 
	//获取屏幕 
	int screen = Launcher.getScreen(); 
	//安装快捷方式 
	if (!installShortcut(context, data, screen)) { 
		//如果屏幕已满，搜寻其他屏幕 
		for (int i = 0; i < Launcher.SCREEN_COUNT; i++) { 
			if (i != screen && installShortcut(context, data, i)) 
				break; 
		} 
	} 
} 
[code]
其中IntallShortcut()方法：首先，对传入的坐标进行判断（findEmptyCell()），如果是空白位置，则可以放置快捷方式；其次，缺省情况下，我们允许创建重复的快捷方式，具体创建过程（addShortcut()）就是把快捷方式的信息传入数据库（addItemToDatabase()）。
（5）UninstallShortcutReceiver：
同理，UninstallShortcutReceiver()继承自BroadcastReceiver()，实现onReceiver()方法。定义一个ContentResolver对象完成对数据库的访问和操作（通过URI定位），进而删除快捷方式。
（6）LauncherProvider：
继承自ContentProvider()，主要是建立一个数据库来存放HomeScreen中的数据信息，并通过内容提供者来实现其他应用对launcher中数据的访问和操作。
重写了ContentProvider()中的方法：
```  
getType()：返回数据类型.如果有自定义的全新类型，通过此方法完成数据的访问。
query()：查询数据.传入URI，返回一个Cursor对象，通过Cursor完成对数据库数据的遍历访问。
Insert()：插入一条数据。
bulkInsert()：大容量数据的插入。
delete()：删除一条数据。
update()：更改一条数据。
sendNotify()：发送通知。
```
类DatabaseHelper继承自一个封装类SQLiteOpenHelper()，方便了数据库的管理和维护。
重写的方法：
```  
onCreate()：创建一个表。其中db.execSQL()方法执行一条SQL语句，通过一条字符串执行相关的操作。当然，对SQL基本语句应该了解。
onUpgrade()：升级数据库。
```
对HomeScreen数据库操作的一些方法：
```  
addClockWidget()，addSearchWidget，addShortcut，addAppShortcut，loadFavorites()，launcherAppWidgetBinder()，convertWidget()，updateContactsShortcuts()，copyFromCursor()
```