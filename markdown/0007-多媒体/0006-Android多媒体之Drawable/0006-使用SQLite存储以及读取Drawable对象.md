在进行Android开发过程中，我们经常会接触到Drawable对象（官方开发文档：A Drawable is a general abstraction for "something that can be drawn."），那么，若要使用数据库来进行存储及读取，该如何实现？
#### 一、存储
```  
//第一步，将Drawable对象转化为Bitmap对象
Bitmap bmp = (((BitmapDrawable)tmp.image).getBitmap());
//第二步，声明并创建一个输出字节流对象
ByteArrayOutputStream os = new ByteArrayOutputStream();
//第三步，调用compress将Bitmap对象压缩为PNG格式，第二个参数为PNG图片质量，第三个参数为接收容器，即输出字节流os
bmp.compress(Bitmap.CompressFormat.PNG, 100, os);
//第四步，将输出字节流转换为字节数组，并直接进行存储数据库操作，注意，所对应的列的数据类型应该是BLOB类型
ContentValues values = new ContentValues();
values.put("image", os.toByteArray());
db.insert("apps", null, values);
db.close();
```
代码看起来比较繁琐，是因为过程的确挺繁琐的，不过可以简单的总结为：
Drawable→Bitmap→ByteArrayOutputStream→SQLite
#### 二、读取
```  
//第一步，从数据库中读取出相应数据，并保存在字节数组中
byte[] blob = cursor.getBlob(cursor.getColumnIndex("image"));
//第二步，调用BitmapFactory的解码方法decodeByteArray把字节数组转换为Bitmap对象
Bitmap bmp = BitmapFactory.decodeByteArray(blob, 0, blob.length);
//第三步，调用BitmapDrawable构造函数生成一个BitmapDrawable对象，该对象继承Drawable对象，所以在需要处直接使用该对象即可
BitmapDrawable bd = new BitmapDrawable(bmp);
```
很显然，读取是存储的相反过程，代码思路也差不多，但实现起来简单很多，总结思路为：
SQLite→byte[]→Bitmap→BitmapDrawable