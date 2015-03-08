总之说了这么多上个例子让大家看看吧！先来个简单的例子，上图：

![img](http://emanual.github.io/md-android/img/media_store/05_mediastore.jpg) 

先说说这个程序的基本框架吧：这个程序有两个线程一个Main负责播放音乐，一个Handler负责更新数据，这个播放器是通过ContentProvider获取存在数据库中的相关信息，然后播放音乐。附上代码：
这里注释挺详细的如果有什么不明白的可以看前几篇文章，里面有详细解释：
```  
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(R.layout.main);
	// 初始化各种控件
	ProceseekBar2 = (SeekBar) findViewById(R.id.seekBar1); // ProceseekBar2是调节播放进度的拖动条
	SoundseekBar = (SeekBar) findViewById(R.id.seekBar2); // SoundseekBar是调节音量的拖动条
	button = (Button) findViewById(R.id.button1);
	nowPlayTime = (TextView) findViewById(R.id.textView1);
	allTime = (TextView) findViewById(R.id.textView2);
	volumeView = (TextView) findViewById(R.id.textView3);
	maxVolumeTextView = (TextView) findViewById(R.id.textView4);
	songNameTV = (TextView) findViewById(R.id.songName);
	songTitleTV = (TextView) findViewById(R.id.songTitle);
	button.setOnClickListener(new ButtonListener());
	// 获取歌曲的相关信息
	getSongInfo();
	// 显示歌曲名称和歌手
	songTitleTV.setText("歌曲名称：" + songTitle);
	songNameTV.setText("歌手：" + songName);
	mediaPlayer = new MediaPlayer();
	audioManager = (AudioManager) getSystemService(AUDIO_SERVICE);
	// 获取最大音量getStreamMaxVolume
	int MaxSound = audioManager.getStreamMaxVolume(AudioManager.STREAM_MUSIC);
	maxVolumeTextView.setText(String.valueOf(MaxSound));
	// 设置音量的最大范围
	SoundseekBar.setMax(MaxSound);
	// 获取当前音量范围getStreamVolume
	int currentSount = audioManager.getStreamVolume(AudioManager.STREAM_MUSIC);
	volumeView.setText(String.valueOf(currentSount));
	SoundseekBar.setProgress(currentSount);
	SoundseekBar.setOnSeekBarChangeListener(new SeekBarListener());
	ProceseekBar2.setOnSeekBarChangeListener(new ProcessBarListener());
}
// 从数据库读取歌曲信息，此处只做了读取数据库中第一首歌曲的信息
private void getSongInfo() {
	ContentResolver cr = getContentResolver();
	 /**
	  * 此处的query是ContentResolver,不是数据库的,因此必须得到一个ContentResolver对象
	  * 返回所有在外部存储卡上的音乐文件的信息 第二个参数Null则返回所有信息
	  */
	Cursor c = cr.query(MediaStore.Audio.Media.EXTERNAL_CONTENT_URI, null,
			null, null, MediaStore.Audio.Media.DEFAULT_SORT_ORDER);
	if (c == null) {
		Toast.makeText(this, "没有歌曲信息", Toast.LENGTH_SHORT).show();
	} else {
		if (c.moveToFirst()) {
			// 获取歌曲的ID
			// int id =
			// c.getInt(c.getColumnIndexOrThrow(MediaStore.Audio.Media._ID));
			// int testid =
			// c.getColumnIndexOrThrow(MediaStore.Audio.Media._ID);
			// // 从此处可以看出歌曲信息的在队列中是从0开始的
			// Toast.makeText(this, "有歌曲信息" + testid,
			// Toast.LENGTH_SHORT).show();
			// 获取歌曲名称
			songTitle = c.getString(c.getColumnIndexOrThrow(MediaStore.Audio.Media.TITLE));
			// 获取歌手名
			songName = c.getString(c.getColumnIndexOrThrow(MediaStore.Audio.Media.ARTIST));
			// 获取播放路径，由于获取的路径为/mnt/sdcard所以要去掉/mnt
			songPath = c.getString(
					c.getColumnIndexOrThrow(MediaStore.Audio.Media.DATA)).substring(4);
			// 获取歌曲时间长度
			// int_TotalTime =
			// c.getInt(c.getColumnIndexOrThrow(MediaStore.Audio.Media.DURATION));
		}
	}
}
class ButtonListener implements OnClickListener {
	@Override
	public void onClick(View arg0) {
		if (mediaPlayer.isPlaying()) {
			mediaPlayer.pause();
			button.setText("播放");
		} else {
			try {
				// 获取歌曲的全部时间并显示
				int Alltime = mediaPlayer.getDuration();
				allTime.setText(ShowTime(Alltime));
				mediaPlayer.reset();
				mediaPlayer.setDataSource(songPath);
				mediaPlayer.prepare();
				mediaPlayer.start();
				button.setText("暂停");
				// 更新各种参数
				StrartbarUpdate();
			} catch (IllegalArgumentException e) {
				e.printStackTrace();
			} catch (IllegalStateException e) {
				e.printStackTrace();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
}
class ProcessBarListener implements OnSeekBarChangeListener {
	@Override
	public void onProgressChanged(SeekBar seekBar, int progress,
			boolean fromUser) {
		if (fromUser == true) {
			// process为此时bar的值，即歌曲播放时间此时的进度
			// seekTo()将播放进度跳到bar的值
			mediaPlayer.seekTo(progress);
			// 同时更新显示时间
			nowPlayTime.setText(ShowTime(progress));
		}
	}
	@Override
	public void onStartTrackingTouch(SeekBar seekBar) {
	}
	@Override
	public void onStopTrackingTouch(SeekBar seekBar) {
	}
}
// 调节音量大小
class SeekBarListener implements OnSeekBarChangeListener {
	@Override
	public void onProgressChanged(SeekBar seekBar, int progress,
			boolean fromUser) {
		if (fromUser) {
			int SeekPosition = seekBar.getProgress();
			audioManager.setStreamVolume(AudioManager.STREAM_MUSIC,
					SeekPosition, 0);
		}
		volumeView.setText(String.valueOf(progress));
	}
	@Override
	public void onStartTrackingTouch(SeekBar seekBar) {
	}
	@Override
	public void onStopTrackingTouch(SeekBar seekBar) {
	}
	// 将ms转换为分秒时间显示函数
	public String ShowTime(int time) {
		// 将ms转换为s
		time /= 1000;
		// 求分
		int minute = time / 60;
		// 求秒
		int second = time % 60;
		minute %= 60;
		return String.format("%02d:%02d", minute, second);
	}

	Handler handler = new Handler();

	public void StrartbarUpdate() {
		// 更新seekBar1
		handler.post(r);
	}
	Runnable r = new Runnable() {
		@Override
		public void run() {
			// 获取歌曲的播放进度是通过MediaPlayer这个类取得的
			// 获取歌曲播放到多少ms
			int CurrentPosition = mediaPlayer.getCurrentPosition();
			// 更新播放时间显示
			nowPlayTime.setText(ShowTime(CurrentPosition));
			// 获取歌曲总时间长度
			int mMax = mediaPlayer.getDuration();
			// 设置bar的最大范围
			ProceseekBar2.setMax(mMax);
			ProceseekBar2.setProgress(CurrentPosition);
			// 每隔100ms更新一次
			handler.postDelayed(r, 100);
		}
	};
	// 退出时做出相应的处理
	@Override
	protected void onDestroy() {
		handler.removeCallbacks(r);
		mediaPlayer.stop();
		mediaPlayer.release();
		super.onDestroy();
	}
}
```
在文章的最后，我会附上一个csdn零积分的下载地址。这里面有详细的注释。并且里面还有一个比较复杂的播放器。先上播放器的图：
![img](http://emanual.github.io/md-android/img/media_store/05_mediastore2.jpg) 
![img](http://emanual.github.io/md-android/img/media_store/05_mediastore3.jpg) 