一 相信每个使用Android系统的人都会知道Android系统中带有一个图库应用程序或者一个音乐播放器吧。打开图库可以查看到当前终端里所有的图片文件，而音乐播放器可以看到当前终端里所有的MP3文件，而这个打开的过程并不会消耗太多的时间。如果是在打开的时候去扫描所有内存，所有SD卡的话，相信相应是不会这么迅速的。
后来通过观察终端的Log，发现每次开机时，会有几条tag为MediaScanner的log信息，顾名思义，这是在扫描媒体库，会不会是这个后台服务实现了图库和音乐的快速相应呢?带着此问题去查阅API，果然发现一个强大的类——MediaStore，通过类名很容易能想到，这个类是用于存放多媒体的。此类包含三个内部类，分别为：
```  
MediaStore.Audio: 存放音频信息
MediaStore.Image: 存放图片信息
MediaStore.Vedio: 存放视频信息
```
上诉三个内部类又有其各自的内部类，鉴于其结构比较复杂，就不详细去描述了，有兴趣的朋友可以结合API自行研究。
这三个内部类存储了多媒体的一些基本信息，并通过ContentProvider的数据共享的机制，将其共享出来，提供给各个应用程序使用。下面的例子展示了一个读取图片信息的示例：　
```  
 public class MainActivity extends Activity {
	private ImageView image;
	private Button btn;
	private int index;
	private int totalCount;
	private ArrayList<String> imageSrcs = new ArrayList<String>();
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		image = (ImageView) findViewById(R.id.image);
		btn = (Button) findViewById(R.id.btn);
		// 获取上下文
		Context ctx = MainActivity.this;
		// 获取ContentResolver对象
		ContentResolver resolver = ctx.getContentResolver();
		// 获得外部存储卡上的图片缩略图
		Cursor c = resolver.query(
				MediaStore.Images.Thumbnails.EXTERNAL_CONTENT_URI, null, null,
				null, null);
		// 为了for循环性能优化，用一变量存储数据条数
		totalCount = c.getCount();
		// 将Cursor移动到第一位
		c.moveToFirst();
		// 将缩略图数据添加到ArrayList中
		for (int i = 0; i < totalCount; i++) {
			int index = c
					.getColumnIndexOrThrow(MediaStore.Images.Thumbnails.DATA);
			String src = c.getString(index);
			imageSrcs.add(src);
			index = i;
			c.moveToNext();
		} // 关闭游标 c.close();
		// 点击按钮，切换图片
		btn.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				String src = imageSrcs.get(index);
				image.setImageURI(Uri.parse(src));
				index++;
				if (index == totalCount) {
					index = 0;
				}
			}
		});
	}
}
```
MediaStore中的存储的信息是通过MediaScannerService这个后台服务维护的，MediaScannerService在接受到系统开机（BOOT_COMPLETED）、媒体挂载(MEDIA_MOUNTED)和扫描指令（MEDIA_SCANNER_SCAN_FILE）广播信息时，即启动MediaScannerService中扫描的相关代码（MediaScanner，此类被@hide隐藏，所以不多介绍）进行扫描和更新MediaStore内的信息。
通过MediaStore和MediaScannerService的配合使用，实现类似系统自带的图库和音乐播放器文件列表功能。
二
MediaStore这个类是android系统提供的一个多媒体数据库，android中多媒体信息都可以从这里提取。这个MediaStore包括了多媒体数据库的所有信息，包括音频，视频和图像,android把所有的多媒体数据库接口进行了封装，所有的数据库不用自己进行创建，直接调用利用ContentResolver去掉用那些封装好的接口就可以进行数据库的操作了。今天我就介绍一些这些接口的用法。
首先，要得到一个ContentResolver实例，ContentResolver可以这样获取，利用一个Activity或者Service的Context即可。如下所示：
```  
ContentResolver mResolver = ctx.getContentResolver();
```
上面的那个ctx的就是一个context，Activity.this就是那个Context，这个Context就相当于一个上下文环境。得到这个Context后就可以调用getContentResolver接口获取ContentResolver实例了。ContentResolver实例获得后，就可以进行各种查询，下面我就以音频数据库为例讲解增删改查的方法，视频和图像和音频非常类似。
在讲解各种查询之前，我给大家介绍下怎么看android都提供了哪些多媒体表。在adb shell中，找到/data/data/com.android.providers.media/databases/下，然后找到SD卡的数据库文件(一般是一个.db文件)，然后输入命令sqlite3加上这个数据库的名字就可以查询android的多媒体数据库了。.table命令可以列出所有多媒体数据库的表，.scheme加上表名可以查询表中的所有列名。这里可以利用SQL语句来查看你想要的数据，记得最后一定要记住每条语句后面都加上分号。下面开始讲述怎么在这些表上进行增删改查。
查询，代码如下所示：
```  
Cursor cursor = resolver.query(_uri, prjs, selections, selectArgs, order);
```
ContentResolver的query方法接受几个参数，参数意义如下:
Uri：这个Uri代表要查询的数据库名称加上表的名称。这个Uri一般都直接从MediaStore里取得，例如我要取所有歌的信息，就必须利用MediaStore.Audio.Media. EXTERNAL _CONTENT_URI这个Uri。专辑信息要利用MediaStore.Audio.Albums.EXTERNAL_CONTENT_URI这个Uri来查询，其他查询也都类似。
Prjs：这个参数代表要从表中选择的列，用一个String数组来表示。
Selections：相当于SQL语句中的where子句，就是代表你的查询条件。
selectArgs：这个参数是说你的Selections里有？这个符号是，这里可以以实际值代替这个问号。如果Selections这个没有？的话，那么这个String数组可以为null。
Order：说明查询结果按什么来排序。
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
delete和更新的方法很类似