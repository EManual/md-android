#### SharedPreferences存储类效率分析
SharedPreferences是Android平台上一个轻量级的存储类，主要是保存一些常用的配置比如窗口状态，一般在Activity中重载窗口状态onSaveInstanceState保存一般使用SharedPreferences完成，它提供了Android平台常规的Long长 整形、Int整形、String字符串型的保存，它是什么样的处理方式呢?
SharedPreferences类似过去Windows系统上的ini配置文件，但是它分为多种权限，可以全局共享访问，提示大家是以xml方式来保存，整体效率来看不是特别的高，对于常规的轻量级而言比SQLite要好不少，如果真的存储量不大可以考虑自己定义文件格式。xml 处理时Dalvik会通过自带底层的本地XML Parser解析，比如XMLpull方式，这样对于内存资源占用比较好。
#### SharedPreferences 的用法
2个activity之间的数据传递除了可以他通过intent来传递,还可以使用SharedPreferences来共享数据的方式
SharedPreferences用法很简单。
在A中设置
```  
Editor sharedata = getSharedPreferences("data", 0).edit();
sharedata.putString("item","hello getSharedPreferences");
sharedata.commit();
```
B中获取
```  
SharedPreferences sharedata = getSharedPreferences("data", 0);
String data = sharedata.getString("item", null);
Log.v("cola","data="+data);
```
Android数据存取之Preferences
这种方式应该是用起来最简单的Android读写外部数据的方法了。他的用法基本上和J2SE(java.util.prefs.Preferences)中的用法一样，以一种简单、透明的方式来保存一些用户个性化设置的字体、颜色、位置等参数信息。一般的应用程序都会提供“设置”或者“首选项”的这样的界面，那么这些设置最后就可以 通过Preferences来保存，而程序员不需要知道它到底以什么形式保存的，保存在了什么地方。当然，如果你愿意保存其他的东西，也没有什么限制。只 是在性能上不知道会有什么问题。
在Android系统中，这些信息以XML文件的形式保存在 /data/data/PACKAGE_NAME /shared_prefs 目录下。
数据读取
```  
String PREFS_NAME = "Note.sample.roiding.com";
SharedPreferences settings = getSharedPreferences(PREFS_NAME, 0);
boolean silent = settings.getBoolean("silentMode", false); 
String hello = settings.getString("hello", "Hi");
```
SharedPreferences settings = getSharedPreferences(PREFS_NAME, 0);
通过名称，得到一个SharedPreferences，顾名思义，这个Preferences是共享的，共享的范围据现在同一个Package中，这里 面说所的Package和Java里面的那个Package不同，貌似这里面的Package是指在
AndroidManifest.xml文件中:
```  
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
	package="com.roiding.sample.note"
	android:versionCode="1"
	android:versionName="1.0.0">
```
这里面的package。根据我目前的实验结果看，是这样的，欢迎指正。后面的那个int是用来声明读写模式，先不管那么多了，暂时就知道设为0(android.content.Context.MODE_PRIVATE)就可以了。 
```  
boolean silent = settings.getBoolean("silentMode", false);
```
获得一个boolean值，这里就会看到用Preferences的好处了：可以提供一个缺省值。也就是说如果Preference中不存在这个值的话，那么就用后面的值作为返回指，这样就省去了我们的if什么什么为空的判断。
数据写入
```  
String PREFS_NAME = "Note.sample.roiding.com";
SharedPreferences settings = getSharedPreferences(PREFS_NAME, 0);
SharedPreferences.Editor editor = settings.edit();
editor.putBoolean("silentMode", true);
editor.putString("hello", "Hello~");
editor.commit();
```
