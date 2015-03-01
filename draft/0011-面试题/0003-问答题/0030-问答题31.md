请介绍下Android的数据存储方式。
答：使用SharedPreferences存储数据；文件存储数据；SQLite数据库存储数据；使用ContentProvider存储数据；网络存储数据；
Preference，File， DataBase这三种方式分别对应的目录是/data/data/Package Name/Shared_Pref, /data/data/Package Name/files, /data/data/Package Name/database 。
#### 一：使用SharedPreferences存储数据
首先说明SharedPreferences存储方式，它是Android提供的用来存储一些简单配置信息的一种机制，例如：登录用户的用户名与密码。其采用了Map数据结构来存储数据，以键值的方式存储，可以简单的读取与写入，具体实例如下：
```  
void ReadSharedPreferences() {
	String strName, strPassword;
	SharedPreferences user = getSharedPreferences("user_info", 0);
	strName = user.getString("NAME", "");
	strPassword = user.getString("PASSWORD", "");
}
void WriteSharedPreferences(String strName, String strPassword) {
	SharedPreferences user = getSharedPreferences("user_info", 0);
	uer.edit();
	user.putString("NAME", strName);
	user.putString("PASSWORD", strPassword);
	user.commit();
}
```
数据读取与写入的方法都非常简单，只是在写入的时候有些区别：先调用edit()使其处于编辑状态，然后才能修改数据，最后使用commit()提交修改的数据。实际上SharedPreferences是采用了XML格式将数据存储到设备中，在DDMS中的File Explorer中的/data/data/<package name>/shares_prefs下。使用SharedPreferences是有些限制的：只能在同一个包内使用，不能在不同的包之间使用。
#### 二：文件存储数据
文件存储方式是一种较常用的方法，在Android中读取/写入文件的方法，与Java中实现I/O的程序是完全一样的，提供了openFileInput()和openFileOutput()方法来读取设备上的文件。具体实例如下:
```  
String fn = "moandroid.log";
FileInputStream fis = openFileInput(fn);
FileOutputStream fos = openFileOutput(fn,Context.MODE_PRIVATE);
```
#### 三：网络存储数据
网络存储方式，需要与Android 网络数据包打交道，关于Android 网络数据包的详细说明，请阅读Android SDK引用了Java SDK的哪些package？。
#### 四：ContentProvider
#### 1、ContentProvider简介
当应用继承ContentProvider类，并重写该类用于提供数据和存储数据的方法，就可以向其他应用共享其数据。虽然使用其他方法也可以对外共享数据，但数据访问方式会因数据存储的方式而不同，如：采用文件方式对外共享数据，需要进行文件操作读写数据；采用sharedpreferences共享数据，需要使用sharedpreferences API读写数据。而使用ContentProvider共享数据的好处是统一了数据访问方式。
#### 2、Uri类简介
Uri代表了要操作的数据，Uri主要包含了两部分信息：1.需要操作的ContentProvider，2.对ContentProvider中的什么数据进行操作，一个Uri由以下几部分组成：
1.scheme：ContentProvider（内容提供者）的scheme已经由Android所规定为：content://…
2.主机名（或Authority）：用于唯一标识这个ContentProvider，外部调用者可以根据这个标识来找到它。
3.路径（path）：可以用来表示我们要操作的数据，路径的构建应根据业务而定，如下：
要操作contact表中id为10的记录，可以构建这样的路径:/contact/10
要操作contact表中id为10的记录的name字段， contact/10/name
要操作contact表中的所有记录，可以构建这样的路径:/contact?
要操作的数据不一定来自数据库，也可以是文件等他存储方式，如下:
要操作xml文件中contact节点下的name节点，可以构建这样的路径：/contact/name
如果要把一个字符串转换成Uri，可以使用Uri类中的parse()方法，如下：
Uri uri = Uri.parse("content://com.changcheng.provider.contactprovider/contact")
#### 3、UriMatcher、ContentUrist和ContentResolver简介
因为Uri代表了要操作的数据，所以我们很经常需要解析Uri，并从 Uri中获取数据。Android系统提供了两个用于操作Uri的工具类，分别为UriMatcher 和ContentUris 。掌握它们的使用，会便于我们的开发工作。
UriMatcher：用于匹配Uri，它的用法如下：
1.首先把你需要匹配Uri路径全部给注册上，如下：
```  
//常量UriMatcher.NO_MATCH表示不匹配任何路径的返回码(-1)。
UriMatcher uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
//如果match()方法匹配content://com.changcheng.sqlite.provider.contactprovider /contact路径，返回匹配码为1
uriMatcher.addURI("com.changcheng.sqlite.provider.contactprovider", "contact", 1);//添加需要匹配uri，如果匹配就会返回匹配码
//如果match()方法匹配 content://com.changcheng.sqlite.provider.contactprovider/contact/230路径，返回匹配码为2
uriMatcher.addURI("com.changcheng.sqlite.provider.contactprovider", "contact/", 2);//号为通配符
```
2.注册完需要匹配的Uri后，就可以使用uriMatcher.match(uri)方法对输入的Uri进行匹配，如果匹配就返回匹配码，匹配码是调用 addURI()方法传入的第三个参数，假设匹配 content://com.changcheng.sqlite.provider.contactprovider/contact路径，返回的匹配码为1。
ContentUris：用于获取Uri路径后面的ID部分，它有两个比较实用的方法：
```  
withAppendedId(uri, id)用于为路径加上ID部分
parseId(uri)方法用于从路径中获取ID部分
```
ContentResolver：当外部应用需要对ContentProvider中的数据进行添加、删除、修改和查询操作时，可以使用ContentResolver类来完成，要获取ContentResolver 对象，可以使用Activity提供的getContentResolver()方法。 ContentResolver使用insert、delete、update、query方法，来操作数据。
