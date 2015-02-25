MediaStore与音乐信息查询
MediaScanner将扫描媒体文件获得的信息全部存储在MediaStore数据库中。MediaStore是基于SQLite数据库系统的，通过ContentProvider方式，程序可以对MediaStore数据库进行增删查改等操作。
MediaStore的数据库文件位于/data/data/com.android.providers/databases,通常可以发现两个数据库文件
internal.db：对应内部存储空间的媒体数据库文件；
external-xxxxxxxx.db：对应外部存储空间的媒体数据文件，由于同一个手机终端可能使用多个SD卡，针对每一个SD卡，OPhone平台都会生成对应的媒体数据库文件。
两个数据库文件除了管理的文件所存储的位置不同外，没有其他区别。本文后续将默认以外部存储为例进行介绍。
使用SQLite命令打开数据库文件，可以看到Android多媒体数据库的基本结构:
```  
>sqlite3external-xxx.db
>.tables
>.schema
```
感兴趣的读者可以自己查看，本文不再一一列举。
MediaStore类是Android平台的多媒体数据库，它包含了音频，视频，图片等所有多媒体文件信息。本文将重点介绍如何管理和获取音频信息，视频和图片等信息的获取与管理与音频类似。
MediaStore以ContentProvider的形式向外提供媒体数据库信息。通过Android平台提供的ContentProvider接口，可以方便的访问数据库信息。
```  
public Cursorquery(Contexctx c, Uri _uri, String[] prjs, String selections,
		String[] selectArgs, String order) {
	ContentResolverresolver = ctx.getContentResolver();
	if (resolver == null) {
		return null;
	}
	returnresolver.query(_uri, prjs, selections, selectArgs, order);
}
```
_uri：指明要查询的数据库名称加上表的名称，从MediaStore中我们可以找到相应信息的参数，具体请参考SDK开发文档。
```  
prjs:指定查询数据库表中的哪几列，返回的游标中将包括相应的信息。Null则返回所有信息。
selection:指定查询条件
selectionArgs：参数selection里有？这个符号时，这里可以以实际值代替这个问号。如果selection这个没有？的话，那么这个String数组可以为null
order：指定查询结果的排列顺序
```
MediaStore.Audio.Media类定义了媒体数据库中的歌曲信息
MediaStore.Audio.Artists类定义了媒体数据库中的歌手信息
MediaStore.Audio.Albums类定义了媒体数据库中的专辑信息
MediaStore.Audio.Playlists类定义了媒体数据库中的播放列表信息
读者可以通过OPhoneSDK开发文档找到详细的信息，结合ContentProvider的查询接口，可以获取所有媒体信息。
MediaPlayer与音乐播放MediaPlayer是Android多媒体编程中最核心的类。它提供了一个多媒体播放器常用的基本操作如播放，暂停，停止，获取文件播放长度等等。它向下通过JNI封装，获取系统提供的多媒体播放能力。
MeidaPlayer提供了设计良好的多媒体接口，播放一个音频文件的步骤非常简单：
1.创建播放器：newMediaPlayer()
2.设置音频源：setDataSource(Audio_PATH)
3.准备音频源：prepare()
4.播放音频：start()
5.停止播放：stop()
6.释放资源：release();
其他MediaPlayer常用接口例如pause(),getDuration(),seekTo()等，大家可以自己查阅SDK文档，这里不在一一介绍。
音频的播放过程也就是MediaPlayer对象的状态转换过程。深入理解MediaPlayer的状态机是灵活驾驭Android多媒体编程的基础。读者在AndroidSDK开发文档中可以查看到MediaPlayer的状态转换图。程序有必要监听这些变化，判断播放器所处的状态，Android平台提供了多种监听器，来监视MediaPlayer的状态变化：
```  
MediaPlayer.OnBufferingUpdateListener
MediaPlayer.OnCompletionListener
MediaPlayer.OnErrorListener
MediaPlayer.OnPreparedListener
MediaPlayer.OnSeekCompleteListener
```
Android平台可以从资源文件、文件系统和网络三种方式来播放多媒体文件。无论使用哪种播放方式，基本的流程都是类似的。
#### 从资源文件播放
多媒体文件可以放在资源文件夹/res/raw目录下，然后通过MediaPlayer.create(Contextctx,intfile)方法创建MediaPlayer对象，获得MediaPlayer对象后直接调用start()方法即可播放音乐。
#### 从文件系统播放
从文件系统播放音乐，需要使用new操作符创建MediaPlayer对象。获得MediaPlayer对象之后，需要依次调用setDataSource()和prepare()方法，以便设置数据源，让播放器完成准备工作，然后调用start()方法播放音乐。
#### 从网络播放
Android平台支持在线播放媒体音乐，通过progressdownload的方式播放在线音频资源。Progressdownload的支持由底层的OpenCore多媒体库提供支持，应用开发者不必关心具体实现细节，只需要设置网络音频资源的地址，就可以完成在线播放的工作，极大提高了开发效率。由于从网络下载播放音频资源需要较长的时间，在准备音频资源的时候，需要使用prepareAsync()方法，这个方法是异步执行的，不会阻塞程序的主进程。MediaPlayer通过MediaPlayer.OnPreparedListener通知MediaPlayer的准备状态。
在特定的情况下，程序需要通过代理服务器访问在线资源，例如，通过APNCMWAP访问网络时，需要设置CMWAP的代理地址（10.0.0.172:80）。Android平台提供了非常方便的解决方案，使开发者可以非常简单的通知OpenCore使用指定的代理来访问网络音频资源：在网络媒体的url后，通过“x-http-proxy”指定代理服务器地址。另外，由于Android平台支持MultiPDP，可以同时建立多个APN连接，所以开发者必须指明哪个连接端口需要使用代理服务器，通过“x-net-interface”指定连接端口，下面的程序简单说明了通过代理服务器播放音频资源的步骤：
```  
private void playFromNetwork() {
	String path = "http://website/path/test.mp3";
	String CMWAP_HOST = "10.0.0.172";
	String CMWAP_PORT = "80";
	// 假设CMWAP连接的端口名
	String SOCKET_INTERFACE = "cminnet0";
	String urlWithProxy = path + "?x-http-proxy=" + CMWAP_HOST + ":
			+ CMWAP_PORT + "&" + "x-net-interface=" + SOCKET_INTERFACE;
	try {
		MediaPlayer player = new MediaPlayer();
		player.setDataSource(path);
		player.setOnPreparedListener(new MediaPlayer.OnPreparedListener() {
			publicvoidonPrepared(MediaPlayer player) {
				player.start();
			}
		});
		player.prepareAsync();
	} catch (Exception e) {
		e.printStackTrace();
	}
}
```