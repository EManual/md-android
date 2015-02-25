Android系统提供了MediaScanner，MediaProvider，MediaStore等接口，并且提供了一套数据库表格，通过Content Provider的方式提供给用户。当手机开机或者有SD卡插拔等事件发生时，系统将会自动扫描SD卡和手机内存上的媒体文件，如audio，video，图片等，将相应的信息放到定义好的数据库表格中。在这个程序中，我们不需要关心如何去扫描手机中的文件，只要了解如何查询和使用这些信息就可以了。
MediaStore中定义了一系列的数据表格，通过ContentResolver提供的查询接口，我们可以得到各种需要的信息。下面我们重点介绍查询SD卡上的音乐文件信息。
先来了解一下ContentResolver的查询接口：
```  
Cursor  query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder)；  
```
Uri：指明要查询的数据库名称加上表的名称，从MediaStore中我们可以找到相应信息的参数，具体请参考开发文档。 
Projection: 指定查询数据库表中的哪几列，返回的游标中将包括相应的信息。Null则返回所有信息。
selection: 指定查询条件
selectionArgs：参数selection里有 ？这个符号是，这里可以以实际值代替这个问号。如果selection这个没有？的话，那么这个String数组可以为null。
SortOrder：指定查询结果的排列顺序
下面的命令将返回所有在外部存储卡上的音乐文件的信息：
```  
Cursor cursor = query(MediaStore.Audio.Media.EXTERNAL_CONTENT_URI, null,null, null, MediaStore.Audio.Media.DEFAULT_SORT_ORDER);  
```
得到cursor后，我们可以调用Cursor的相关方法具体的音乐信息:
```  
歌曲ID：MediaStore.Audio.Media._ID 
Int id = cursor.getInt(cursor.getColumnIndexOrThrow(MediaStore.Audio.Media._ID));  
歌曲的名称 ：MediaStore.Audio.Media.TITLE
String tilte = cursor.getString(cursor.getColumnIndexOrThrow(MediaStore.Audio.Media.TITLE));  
歌曲的专辑名：MediaStore.Audio.Media.ALBUM 
String album = cursor.getString(cursor.getColumnIndexOrThrow(MediaStore.Audio.Media.ALBUM));  
歌曲的歌手名： MediaStore.Audio.Media.ARTIST 
String artist = cursor.getString(cursor.getColumnIndexOrThrow(MediaStore.Audio.Media.ARTIST));  
歌曲文件的路径 ：MediaStore.Audio.Media.DATA 
String url = cursor.getString(cursor.getColumnIndexOrThrow(MediaStore.Audio.Media.DATA));  
歌曲的总播放时长 ：MediaStore.Audio.Media.DURATION
Int duration = cursor.getInt(cursor.getColumnIndexOrThrow(MediaStore.Audio.Media.DURATION));  
歌曲文件的大小 ：MediaStore.Audio.Media.SIZE 
Int size = cursor.getLong(cursor.getColumnIndexOrThrow(MediaStore.Audio.Media.SIZE)); 
```