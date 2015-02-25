Android平台上的媒体文件管理和桌面系统不同。在桌面系统上，不同目录下的媒体文件呈树状结构显示给用户，用户需要进入不同目录寻找该目录下的文件。而在Android平台上，不同目录下的媒体文件则以一层列表方式显示给用户，用户不需进入子目录就可以列出（某种类型的）所有媒体文件。
在Android上，为了实现这种模式的媒体文件管理，对所有管理的媒体文件抽取其元数据,也就是ID3（mp3文件包含的元数据可参考http://en.wikipedia.org/wiki/ID3），存储在数据库中，并作为一个content provider提供给其他应用使用。用户的每一次显示媒体文件的操作，就是对这个数据库的一次查询操作。在多媒体管理模块中，主要分成三个模块：
#### 多媒体数据库
MediaStore这个类是android系统提供的一个多媒体数据库，android中多媒体信息都可以从这里提取。这个MediaStore包括了多媒体数据库的所有信息，包括音频，视频和图像,android把所有的多媒体数据库接口进行了封装，所有的数据库不用自己进行创建，直接调用利用ContentResolver去掉用那些封装好的接口就可以进行数据库的操作，多媒体数据库的使用方法和SQLITE3的方法是一样的。
MediaStore中的数据是在MediaScanner扫描后通过MediaProvider中的一个service进行更新的。
#### MediaScanner
在Android系统中，多媒体库是通过MediaScanner去扫描磁盘文件，对元信息的处理，并通过MediaProvider保存到MediaStore中。
MediaScanner可以通过手动控制，在ANDROID系统中，已经定制了三种事件会触发MediaScanner去扫描磁盘文件：ACTION_BOOT_COMPLETED、ACTION_MEDIA_MOUNTED、ACTION_MEDIA_SCANNER_SCAN_FILE。其中ACTION_BOOT_COMPLETED是系统启动完后发出这个消息，ACTION_MEDIA_MOUNTED是插卡事件触发的消息，ACTION_MEDIA_SCANNER_SCAN_FILE消息一般是在一些文件操作后，开发人员手动发出的一个重新扫描多媒体文件的消息。发送消息通过sendBroadcast函数完成，比如广播一个ACTION_MEDIA_MOUNTED消息：
```  
sendBroadcast(new Intent(Intent.ACTION_MEDIA_MOUNTED, Uri.parse("file://" + Environment.getExternalStorageDirectory())));
```
由上可知是通过发送了一个广播（传递对应的扫描要求）来触发重新扫描磁盘事件，那么可以猜测系统肯定存在一个广播接收器（何时何地注册？），在收到这个广播消息后，通过对应参数启动MediaScannerService。MediaScannerService调用一个公用类MediaScanner去处理真正的工作。MediaScannerReceiver维持两种扫描目录：一种是内部卷（internal volume）指向$(ANDROID_ROOT)/media. 另一种是外部卷（external volume）指向$(EXTERNAL_STORAGE)，扫描的位置可以修改（一般外部不用修改，默认为SDCARD，内部根据驱动命名的INAND路经名做对应的修改），对应图1-1系统源码具体位置：
```  
MediaScannerReceiver：/mydroid/packages/providers/MediaProvider/src/com/android/providers/media/MediaScannerReceiver.java
```
Scanner事件接收，继承了BroadcastReceiver类，收到扫描消息后启动MediaScannerService，但有一点要注意的是： Service的onCreate的方法只会被调用一次，就是你无论多少次的startService又 bindService，Service只被创建一次。如果先是bind了，那么start的时候就直接运行Service的 onStart方法，如果先是start，那么bind的时候就直接运行onBind方法。如果你先bind上了，就stop不掉了，对啊，就是stopService不好使了，只能先UnbindService, 再StopService,所以是先start还是先bind行为是有区别的。（关于BINDLE接口请参考其它文档）
MediaScannerService：
```  
/mydroid/packages/providers/MediaProvider/src/com/android/providers/media/MediaScannerService.java
```
通过此服务，去调用MediaScanner的具体实现方法。
MediaScanner:  (方法)
```  
\frameworks\base\media\java\android\media\ MediaScanner.java
```
#### Thumbnails缩略图概述
缩略图保存位置：/sdcard/DCIM/.thumbnails
缩略图（大）：一张张的JPEG文件
所有的都存入同一个二进制文件.thumbdata+version+”-”+hashcode，字节大小均为 10000bytes
#### 详解：
对于每一张图片，都会生成大缩略图和小缩略图，大缩略图保存在（外部设备）/sdcard/DCIM/.thumbnails/ 目录下，大缩略图大小上限是512 X 384,下限1 X1。小缩略图被统一保存到一个随机访问文件 （外部设备）/sdcard/DCIM/.thumbnails/ +".thumbdata" + version + "-"+ mUri.hashcode()
#### 缩略图存储到数据库里面
扫描一个文件的最后endfile()会mMediaProvider.insert()或者mMediaProvider.update()。在mMediaProvider.insert()时对于IMAGES_MEDIA 和VIDEO_MEDIA类型：requestMediaThumbnail()--->mCurrentThumbRequest.execute(); 在执行时会首先会去缩略图的数据库中查询，查询到就返回，未查询到会ThumbnailUtil.createVideoThumbnail(mPath)或者 ThumbnailUtil.createImageThumbnail(mCr, mPath, mUri, mOrigId,Images.Thumbnails.MICRO_KIND, true/*saveMini*/));
#### 创建图片缩略图：
创建thumbnail时调用ThumbnailUtil.makeBitmap()创建；如果saveMini为真会保存缩略图ThumbnailUtil.storeThumbnail(cr, origId, thumbData, bitmap.getWidth(), bitmap.getHeight());; 保存时会通过origId向数据库查询，查到返回对应的uri，未查找到就插入数据库并返回uri，最终返回bitmap。对于创建视频缩略图：直接在视频文件中取一帧得到bintmap并返回保存大缩略图到文件： bitmap.compress(Bitmap.CompressFormat.JPEG, 75, miniOutStream);保存小缩略图到随机访问文件：data = miniOutStream.toByteArray(); miniThumbFile.saveMiniThumbToFile(data, mOrigId, magic);
在mMediaProvider.update()时，对IMAGES_MEDIA，IMAGES_MEDIA_ID，VIDEO_MEDIA，VIDEO_MEDIA_ID类型会查询magic，如果没查到会执行requestMediaThumbnail()，流程同上。
MediaProvider是对上面整个流程的实现。可参考里面的代码。
转自：http://wenku.baidu.com/view/537b9b8ecc22bcd126ff0c97.html
对Android稍有熟悉的人都知道，Android Media Scanner只对SD卡上的媒体文件进行扫描。其扫描的策略，请参考《Android Media Scanner Process》。假如我们的硬件平台上面没有提供SD卡槽，难道Android就不能进行对媒体文件播放了吗？当然不是的，否则Android系统将不会成为一个完善的Framework。本文结合本人的经验介绍一下，怎样修改多媒体文件的扫描路径。
根据《Android Media Scanner Process》的介绍我们可以知道，Android scanner扫描媒体完成之后，会把媒体文件存放在数据库中，由MediaProvider为上层的应用程序提供服务。
经过研究Media scanner的代码发现，他的扫描路径为android.os.Environment.EXTERNAL_STORAGE_DIRECTORY。定义该变量文件位于：
```  
frameworks/base/core/java/android/os/Environment.java
```
默认情况下，Android将会搜索/sddisk目录：
```  
private static final File EXTERNAL_STORAGE_DIRECTORY = getDirectory("EXTERNAL_STORAGE", "/sddisk");
```
为了让其进行搜索我们自定义的路径，可以修改该变量的定义，加入我们希望扫描/external目录。代码修改如下：
private static final File EXTERNAL_STORAGE_DIRECTORY = getDirectory("EXTERNAL_STORAGE", "/external");
这样Android Media Scanner将会搜索/external目录来查找媒体文件。
下一步我们需要保证这个文件一定要存在，我们需要修改init.rc文件。增加如下的定义：
```  
mkdir /external 0777 system system
```
这样在开机的时候，如果/external目录不存在，则会创建一个。如果已经存在，则不会有任何动作。
另外怎样触发Media Scanner？根据《Android Media Scanner Process》的介绍，当收到ACTION_BOOT_COMPLETED，ACTION_MEDIA_MOUNTED，ACTION_MEDIA_SCANNER_SCAN_FILE消息的时候才会进行扫描。以前是扫描SD卡，当SD卡mount的时候Android系统会有ACTION_MEDIA_MOUNTED消息通知，Media Scanner开始扫描媒体文件。但是我们的/external目录修改之后，怎样通知Android media scanner扫描呢？一个办法是重启，没有人乐意这样做。另外一个办法是运行menu->dev tools->Media Scan，这样将会进行扫描。目前我还没有让目录修改之后，自动扫描的办法。如果你有好的点子，请你给我留言。
通过以上的步骤，可以在Android的/external目录存放媒体文件，并且被music应用程序播放了。