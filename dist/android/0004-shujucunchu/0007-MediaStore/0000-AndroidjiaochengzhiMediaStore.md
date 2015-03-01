今天给大家讲android的多媒体数据库。MediaStore这个类是android系统提供的一个多媒体数据库，android中多媒体信息都可以从这里提取。这个MediaStore包括了多媒体数据库的所有信息，包括音频，视频和图像,android把所有的多媒体数据库接口进行了封装，所有的数据库不用自己进行创建，直接调用利用ContentResolver去掉用那些封装好的接口就可以进行数据库的操作了。今天我就介绍一些这些接口的用法。首先，要得到一个ContentResolver实例，ContentResolver可以这样获取，利用一个Activity或者Service的Context即可。如下所示：
```  
ContentResolver mResolver = ctx.getContentResolver();
```
上面的那个ctx的就是一个context，Activity.this就是那个Context，这个Context就相当于一个上下文环境。得到这个Context后就可以调用getContentResolver接口获取ContentResolver实例了。ContentResolver实例获得后，就可以进行各种查询，下面我就以音频数据库为例讲解增删改查的方法，视频和图像和音频非常类似。
在讲解各种查询之前，我给大家介绍下怎么看android都提供了哪些多媒体表。在adbshell中，找到/data/data/com.android.providers.media/databases/下，然后找到SD卡的数据库文件(一般是一个.db文件)，然后输入命令sqlite3加上这个数据库的名字就可以查询android的多媒体数据库了。.table命令可以列出所有多媒体数据库的表，.scheme加上表名可以查询表中的所有列名。这里可以利用SQL语句来查看你想要的数据，记得最后一定要记住每条语句后面都加上分号。下面开始讲述怎么在这些表上进行增删改查。
查询，代码如下所示：
```  
Cursor cursor = resolver.query(_uri, prjs, selections, selectArgs, order);
```
ContentResolver的query方法接受几个参数，参数意义如下:
```  
Uri：这个Uri代表要查询的数据库名称加上表的名称。这个Uri一般都直接从MediaStore里取得，例如我要取所有歌的信息，就必须利用MediaStore.Audio.Media. EXTERNAL_CONTENT_URI这个Uri。专辑信息要利用MediaStore.Audio.Albums.EXTERNAL_CONTENT_URI这个Uri来查询，其他查询也都类似。
Prjs：这个参数代表要从表中选择的列，用一个String数组来表示。
Selections：相当于SQL语句中的where子句，就是代表你的查询条件。
selectArgs：这个参数是说你的Selections里有？这个符号是，这里可以以实际值代替这个问号。如果Selections这个没有？的话，那么这个String数组可以为null。
Order：说明查询结果按什么来排序。
```
上面就是各个参数的意义，它返回的查询结果一个Cursor，这个Cursor就相当于数据库查询的中Result，用法和它差不多。
增加，代码如下所以：
```  
ContentValues values = new ContentValues();
values.put(MediaStore.Audio.Playlists.Members.PLAY_ORDER,0);
resolver.insert(_uri, values);
```
这个insert传递的参数只有两个，一个是Uri(同查询那个Uri)，另一个是ContentValues。这个ContentValuses对应于数据库的一行数据，只要用put方法把每个列的设置好之后，直接利用insert方法去插入就好了。
更新，代码如下：
```  
ContentResolver resolver = ctx.getContentResolver();
Uri uri = MediaStore.Audio.Media.EXTERNAL_CONTENT_URI;
ContentValues values = new ContentValues();
values.put(MediaStore.Audio.Media.DATE_MODIFIED, sid);
resolver.update(MediaStore.Audio.Playlists.EXTERNAL_CONTENT_URI,values, where, selectionArgs);
```
上面update方法和查询还有增加里的参数都很类似，这里就不再重复叙述了，大家也可直接参考google的文档，那里也写的很清楚。
删除，代码如下：
```  
ContentResolver resolver = ctx.getContentResolver();  
resolver.delete(MediaStore.Audio.Playlists.EXTERNAL_CONTENT_URI,where, selectionArgs);
```
delete和更新的方法很类似，大家对照更新的方法看下马上就会明白。