通过以下的方法可以得到的是卡中所有图片的缩略图，另外可以使用异步加载，提高速度：
```  
String[] projection = { MediaStore.Images.Media.SIZE, MediaStore.Images.Media.DISPLAY_NAME };
Uri uri = MediaStore.Images.Thumbnails.getContentUri("external");
Cursor c = Thumbnails.queryMiniThumbnails(getContentResolver(), uri, Thumbnails.MINI_KIND, null);
```
第二行的代码意思为取得sdcard的路径Uri
```  
Uri uri = MediaStore.Images.Thum
```