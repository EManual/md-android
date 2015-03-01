游戏开发中,通过资料和书籍了解到在有两种播放音频形式可以用在我们的游戏开发中,第一个：MediaPlayer 类 ；第二个：SoundPool 类!
PS:当然还有一个JetPlayer 但是播放的文件格式比较麻烦，所以这里抛开不解释，有兴趣的可以去自己研究下、呵呵;
运行效果图：
![img](P)  
MediaPlayer 和：SoundPool 类!那么他们之间的利弊各是什么呢？或者说，我们游戏开发到底用哪一个更佳呢？答案就是：两者都必须要！！！分析利弊与各自的用途后,等各位童鞋熟习每个播放形式实现之后我会详细道来！ 
下面仍然是先上代码：(先看代码，然后我讲解两个播放形式的利弊关系和各个用途以及其中解释代码中的几个备注!)
```  
import java.util.HashMap;
import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.media.AudioManager;
import android.media.MediaPlayer;
import android.media.SoundPool;
import android.view.KeyEvent;
import android.view.MotionEvent;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.SurfaceHolder.Callback;
public class MySurfaceView extends SurfaceView implements Callback, Runnable {
	private Thread th;
	private SurfaceHolder sfh;
	private Canvas canvas;
	private MediaPlayer player;
	private Paint paint;
	private boolean ON = true;
	private int currentVol, maxVol;
	private AudioManager am;
	private HashMap<Integer, Integer> soundPoolMap;// 备注1
	private int loadId;
	private SoundPool soundPool;
	public MySurfaceView(Context context) {
		super(context);
		// 获取音频服务然后强转成一个音频管理器,后面方便用来控制音量大小用
		am = (AudioManager) MainActivity.instance
				.getSystemService(Context.AUDIO_SERVICE);
		maxVol = am.getStreamMaxVolume(AudioManager.STREAM_MUSIC);
		// 获取最大音量值（15最大! .不是100！）
		sfh = this.getHolder();
		sfh.addCallback(this);
		th = new Thread(this);
		this.setKeepScreenOn(true);
		setFocusable(true);
		paint = new Paint();
		paint.setAntiAlias(true);
		// MediaPlayer的初始化
		player = MediaPlayer.create(context, R.raw.himi);
		player.setLooping(true);// 设置循环播放
		// SoundPool的初始化
		soundPool = new SoundPool(4, AudioManager.STREAM_MUSIC, 100);
		soundPoolMap = new HashMap<Integer, Integer>();
		soundPoolMap.put(1,
				soundPool.load(MainActivity.content, R.raw.himi_ogg, 1));
		loadId = soundPool.load(context, R.raw.himi_ogg, 1);
		// load()方法的最后一个参数他标识优先考虑的声音。目前没有任何效果。使用了也只是对未来的兼容性价值。
	}
	public void surfaceCreated(SurfaceHolder holder) {
		/*
		  * Android OS中，如果你去按手机上的调节音量的按钮，会分两种情况，
		  * 一种是调整手机本身的铃声音量，一种是调整游戏，软件，音乐播放的音量 当我们在游戏中的时候 ，总是想调整游戏的音量而不是手机的铃声音量，
		  * 可是烦人的问题又来了，我在开发中发现，只有游戏中有声音在播放的时候 ，你才能去调整游戏的音量，否则就是手机的音量，有没有办法让手机只要是
		  * 在运行游戏的状态就只调整游戏的音量呢？试试下面这段代码吧!
		  */
		MainActivity.instance.setVolumeControlStream(AudioManager.STREAM_MUSIC);
		// 设定调整音量为媒体音量,当暂停播放的时候调整音量就不会再默认调整铃声音量了，娃哈哈

		player.start();
		th.start();
	}
	public void draw() {
		canvas = sfh.lockCanvas();
		canvas.drawColor(Color.WHITE);
		paint.setColor(Color.RED);
		canvas.drawText("当前音量: " + currentVol, 100, 40, paint);
		canvas.drawText("当前播放的时间" + player.getCurrentPosition() + "毫秒", 100,
				70, paint);
		canvas.drawText("方向键中间按钮切换 暂停/开始", 100, 100, paint);
		canvas.drawText("方向键←键快退5秒 ", 100, 130, paint);
		canvas.drawText("方向键→键快进5秒 ", 100, 160, paint);
		canvas.drawText("方向键↑键增加音量 ", 100, 190, paint);
		canvas.drawText("方向键↓键减小音量", 100, 220, paint);
		sfh.unlockCanvasAndPost(canvas);
	}
	private void logic() {
		currentVol = am.getStreamVolume(AudioManager.STREAM_MUSIC);// 不断获取当前的音量值
	}
	@Override
	public boolean onKeyDown(int key, KeyEvent event) {
		if (key == KeyEvent.KEYCODE_DPAD_CENTER) {
			ON = !ON;
			if (ON == false)
				player.pause();
			else
				player.start();
		} else if (key == KeyEvent.KEYCODE_DPAD_UP) {// 按键这里本应该是RIGHT,但是因为当前是横屏模式,以下雷同
			player.seekTo(player.getCurrentPosition() + 5000);
		} else if (key == KeyEvent.KEYCODE_DPAD_DOWN) {
			if (player.getCurrentPosition() < 5000) {
				player.seekTo(0);
			} else {
				player.seekTo(player.getCurrentPosition() - 5000);
			}
		} else if (key == KeyEvent.KEYCODE_DPAD_LEFT) {
			currentVol += 1;
			if (currentVol > maxVol) {
				currentVol = 100;
			}
			am.setStreamVolume(AudioManager.STREAM_MUSIC, currentVol,// 备注2
					AudioManager.FLAG_PLAY_SOUND);
		} else if (key == KeyEvent.KEYCODE_DPAD_RIGHT) {
			currentVol -= 1;
			if (currentVol <= 0) {
				currentVol = 0;
			}
			am.setStreamVolume(AudioManager.STREAM_MUSIC, currentVol,
					AudioManager.FLAG_PLAY_SOUND);
		}
		soundPool.play(loadId, currentVol, currentVol, 1, 0, 1f);// 备注3
		// soundPool.play(soundPoolMap.get(1), currentVol, currentVol, 1, 0,
		// 1f);//备注4
		// soundPool.pause(1);//暂停SoundPool的声音
		return super.onKeyDown(key, event);
	}
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		return true;
	}
	public void run() {
		while (true) {
			draw();
			logic();
			try {
				Thread.sleep(100);
			} catch (Exception ex) {
			}
		}
	}
	public void surfaceChanged(SurfaceHolder holder, int format, int width,
			int height) {
	}

	public void surfaceDestroyed(SurfaceHolder holder) {
	}
}
```
#### 一、 MediaPlayer 播放音频的实现步骤：
1. 调用MediaPlayer.create(context, R.raw.himi);利用MediaPlayer类调用create方法并且传入通过id索引的资源音频文件，得到实例;
2. 得到的实例就可以调用 
MediaPlayer.star();简单吧、其实MediaPlayer还有几个构造方法，大家有兴趣可以去尝试和实现，这里主要是简单的向大家介绍基本的，毕竟简单实用最好！ 
#### 二、 SoundPlayer 播放音频的实现步骤：
1. new出一个实例 ;   new SoundPool(4, AudioManager.STREAM_MUSIC, 100);第一个参数是允许有多少个声音流同时播放,第2个参数是声音类型,第三个参数是声音的品质;
2. loadId = soundPool.load(context, R.raw.himi_ogg, 1);
3. 使用实例调用play方法传入对应的音频文件id即可！ 下面讲下两个播放形式的利弊：    
使用MediaPlayer来播放音频文件存在一些不足:例如：资源占用量较高、延迟时间较长、不支持多个音频同时播放等。这些缺点决定了MediaPlayer在某些场合的使用情况不会很理想，例如在对时间精准度要求相对较高的游戏开发中。最开始我使用的也是普通的MediaPlayer的方式,但这个方法不适合用于游戏开发,因为游戏里面同时播放多个音效是常有的事，用过MediaPlayer的朋友都该知道，它是不支持实时播放多个声音的，会出现或多或少的延迟，而且这个延迟是无法让人忍受的，尤其是在快速连续播放声音(比如连续猛点按钮)时，会非常明显，长的时候会出现3~5秒的延迟，【使用MediaPlayer.seekTo() 这个方法来解决此问题】;    
相对于使用SoundPool存在的一些问题:
1.SoundPool最大只能申请1M的内存空间，这就意味着我们只能使用一些很短的声音片段，而不是用它来播放歌曲或者游戏背景音乐（背景音乐可以考虑使用JetPlayer来播放）。 
2.SoundPool提供了pause和stop方法，但这些方法建议最好不要轻易使用，因为有些时候它们可能会使你的程序莫名其妙的终止。还有些朋友反映它们不会立即中止播放声音，而是把缓冲区里的数据播放完才会停下来，也许会多播放一秒钟。
3.音频格式建议使用OGG格式。使用WAV格式的音频文件存放游戏音效，经过反复测试，在音效播放间隔较短的情况下会出现异常关闭的情况（有说法是SoundPool目前只对16bit的WAV文件有较好的支持）。后来将文件转成OGG格式，问题得到了解决。
4.在使用SoundPool播放音频的时候，如果在初始化中就调用播放函数进行播放音乐那么根本没有声音，不是因为没有执行，而是SoundPool需要一准备时间！囧。当然这个准备时间也很短，不会影响使用，只是程序一运行就播放会没有声音罢了，所以我把SoundPool播放写在了按键中处理了、备注4的地方大概看完了利弊解释，那么来看我的代码备注的地方：
备注1：这里我定义了一个HashMap,这个是哈希表，如果大家不是很了解这个类，那建议百度，google学习下，它与Hashtable很常用的,它俩的主要区别是：HashMap不同步、空键值、效率高;Hashtable同步、非空键值、效率略低;而在J2ME中不支持HashMap，因为me中不支持空键值,所以在me中只能使用hashtable、咳咳、言归正传，我这里使用hashmap主要是为了存入多个音频的ID,播放的时候可以同时播放多个音频。
上面也介绍了，SoundPool可以支持多个音频同时播放，而且SoundPool在播放的时候调用的这个方法(备注3)soundPool.play(loadId,currentVol,currentVol,1,0,1f);第一个参数指的就是之前的loadId！是通过soundPool.load(context,R.raw.himi_ogg,1);方法取出来的，那么除此之外还要注意一点的就是定义hashmap的时候一定要定义成这种形式HashMap<Integer, Integer> hm = new Hash<Integer,Integer>,声明此哈希表就是一个key和volue值都是Integer的哈希表！ 
为什么要这么做，因为如果你只是简单的定义成 HashMap hm = newHashMap()，那么当你在播放的时候,也就是备注4方法这里的第一个id参数使用Hashmap.get()这个方法的时候总会出现错误的提示！
《SoundPool最大只能申请1M的内存空间，这就意味着我们只能使用一些很短的声音片段》为什么只能使用一些很短的声音呢？
大家还是看备注4方法的第一个参数，这里要求传入的Id类型是个int值，那么这个int其实对应的是通过load()方法返回的音频id，而且这个id会因音频文件的大小而变大变小，那么一旦我们的音频文件超过int最大值，那么就会报内存错误的异常。所以为什么用SoundPool只能播放一些简短的音频这就是其原因了。当然os里为什么这么定义，我也无从查证和说明。
备注4 ：此方法中参数的解释
第一个参数是我通过SoundPool.load()方法返回的音频对应id，第二个第三个参数表示左右声道大小，第四个参数是优先级，第五个参数是循环次数，最后一个是播放速率（1.0 =正常播放，范围是0.5至2.0）
备注2: 
这里是通过媒体服务得到一个音频管理器，从而来对音量大小进行调整。这里要强调一下，调整音频是用这个音频管理器调用setStreamVolume()的方式去调整，而不是MediaPlayer.setVolue(int LeftVolume,int RightVolume);这个方法的两个参数也是调正左右声道而不是调节声音大小。  
好了，对此我们对游戏开发中到底需要用什么来做进行了分析，总结就是SoundPool适合做特效声，其实播放背景音乐我感觉还是用MediaPlayer比较好，当然啦，用什么都看大家喜好和选择啦！下面附上项目下载地址：(项目10+MB因为含有res音频文件)
有人问怎么才知道一首歌曲播放完了，那么这里给说下：
PlaybackCompleted状态：文件正常播放完毕，而又没有设置循环播放的话就进入该状态，并会触发OnCompletionListener的onCompletion()方法。此时可以调用start()方法重新从头播放文件，也可以stop()停止MediaPlayer，或者也可以seekTo()来重新定位播放位置。