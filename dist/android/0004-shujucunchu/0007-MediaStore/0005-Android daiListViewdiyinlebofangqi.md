带ListView的音乐播放器
[功能]
今天说的是：通过MediaStore 来得到目标的Uri 在把之传入MediaPlay 然后再播放
所以会有2个重点：
```  
* 列出emulator 的所有音乐文件　　
* 音乐播放器
```
1. 构建界面：main.xml
写道：1 Button 用于音乐播放控制(暂停/继续)；1 TextView 用于显示目标Uri；1 ListView 用于列出所有音乐文件
```  ：
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal" >
        <Button
            android:id="@+id/cmd"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Prepare" />
        <TextView
            android:id="@+id/name"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:text="NAME" />
    </LinearLayout>
    <ListView
        android:id="@+id/list"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
</LinearLayout>
```
2. 列出所有音乐文件 并转入 adapter
```  ：
Cursor c = getContentResolver().query(MediaStore.Audio.Media.EXTERNAL_CONTENT_URI, null, null, null, null);
String[] from = { MediaStore.Audio.AudioColumns.TITLE, MediaStore.Audio.AudioColumns.ARTIST };
int[] to = { android.R.id.text1, android.R.id.text2 };
final CursorAdapter adapter = new SimpleCursorAdapter(this, android.R.layout.simple_list_item_2, c, from, to);
Cursor c = getContentResolver().query(MediaStore.Audio.Media.EXTERNAL_CONTENT_URI, null, null, null, null);
String[] from = { MediaStore.Audio.AudioColumns.TITLE, MediaStore.Audio.AudioColumns.ARTIST };
int[] to = { android.R.id.text1, android.R.id.text2 };
final CursorAdapter adapter = new SimpleCursorAdapter(this, android.R.layout.simple_list_item_2, c, from, to);
```
3. 使用之
```  
list.setAdapter(adapter);
list.setAdapter(adapter);
```
4. 与音乐播放有关的功能
```  
//1. 定义   
MediaPlayer mp;   
//2. 初始化   
mp = new MediaPlayer();   
//3. 暂停   
mp.pause();   
//4. 继续   
mp.start();   
//5. 判断是否正在播放   
mp.isPlaying()   
//6.使用目标Uri   
mp.release();   
mp = MediaPlayer.create(this, uri);   
//1. 定义   
MediaPlayer mp;   
//2. 初始化   
mp = new MediaPlayer();   
//3. 暂停   
mp.pause();   
//4. 继续   
mp.start();   
//5. 判断是否正在播放   
mp.isPlaying()   
//6.使用目标Uri   
mp.release();   
mp = MediaPlayer.create(this, uri);  
```
5. 单击ListView中某个Item 会播放目标音乐资源
```  
public void playMusic(long arg3) throws IllegalArgumentException,
		IllegalStateException, IOException {
	Uri uri = Uri.withAppendedPath(
			MediaStore.Audio.Media.EXTERNAL_CONTENT_URI,
			String.valueOf(arg3));
	TextView tv = (TextView) findViewById(R.id.name);
	tv.setText(uri.toString());
	mp.release();
	mp = MediaPlayer.create(this, uri);
	mp.start();
	list.setOnItemClickListener(new OnItemClickListener() {
		@Override
		public void onItemClick(AdapterView arg0, View arg1, int arg2,
				long arg3) {
			try {
				playMusic(arg3);
			} catch (IllegalArgumentException e) {
				e.printStackTrace();
			} catch (IllegalStateException e) {
				e.printStackTrace();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	});
}
```
因为这是个音乐播放器界面，也没什么特别之处，就此略过只有一个关于所有音乐文件的ListView