#### SharedPreferences 的用法
2 个activity 之间的数据传递除了可以他通过intent 来传递,还可以使用SharedPreferences 来共享数据的方式SharedPreferences 用法很简单。
在A 中设置
```  
Editor sharedata = getSharedPreferences("data", 0).edit();
sharedata.putString("item","hello getSharedPreferences");
sharedata.commit();
```
B 中获取
```  
SharedPreferences sharedata = getSharedPreferences("data", 0);
String data = sharedata.getString("item", null);
Log.v("cola","data="+data);
```
#### Android数据存取之Preferences
一般的应用程序都会提供“设置”或者“首选项”的这样的界面，那么这些设置最后就可以 通过
Preferences 来保存，而程序员不需要知道它到底以什么形式保存的，保存在了什么地方。
在Android 系统中，文件的形式保存在/data/data/PACKAGE_NAME /shared_prefs 目录下.
#### 数据读取
```  
String PREFS_NAME = "Note.sample.roiding.com";
SharedPreferences settings = getSharedPreferences(PREFS_NAME, 0);
boolean silent = settings.getBoolean("silentMode", false);
String hello = settings.getString("hello", "Hi");
```
SharedPreferences settings = getSharedPreferences(PREFS_NAME, 0);
通过名称，得到一个SharedPreferences,这个Preferences是共享的,共享的范围据现在同一个Package中
#### 数据写入
```  
String PREFS_NAME = "Note.sample.roiding.com";
SharedPreferences settings = getSharedPreferences(PREFS_NAME, 0);
SharedPreferences.Editor editor = settings.edit();
editor.putBoolean("silentMode", true);
editor.putString("hello", "Hello~");
editor.commit();
```