代码如下：
```  
import android.content.Context;
import android.net.Uri;
import android.os.PowerManager;
import android.os.SystemClock;
import android.util.Log;
import java.io.IOException;
import java.lang.IllegalStateException;
import java.util.LinkedList;
public class AsyncPlayer {
	private static final int PLAY = 1;
	private static final int STOP = 2;
	private static final boolean mDebug = false;
	private static final class Command {
		int code;
		Context context;
		Uri uri;
		boolean looping;
		int stream;
		long requestTime;
		public String toString() {
			return "{ code=" + code + " looping=" + looping + " stream=
					+ stream + " uri=" + uri + " }";
		}
	}
	private LinkedList<Command> mCmdQueue = new LinkedList(); // 用一个链表保存播放参数队列
	private void startSound(Command cmd) {
		try {
			MediaPlayer player = new MediaPlayer();
			player.setAudioStreamType(cmd.stream);
			player.setDataSource(cmd.context, cmd.uri); // 设置媒体源，这里Android123提示大家本类的public
														// void play (Context
														// context, Uri uri,
														// boolean looping, int
														// stream)
														// 类第二个参数Uri为媒体位置。
			player.setLooping(cmd.looping);
			player.prepare();
			player.start();
			if (mPlayer != null) {
				mPlayer.release();
			}
			mPlayer = player;
		} catch (IOException e) {
			Log.w(mTag, "error loading sound for " + cmd.uri, e);
		} catch (IllegalStateException e) {
			Log.w(mTag, "IllegalStateException (content provider died?)
					+ cmd.uri, e);
		}
	}
	private final class Thread extends java.lang.Thread { // 通过多线程方式不阻塞调用者
		Thread() {
			super("AsyncPlayer-" + mTag);
		}
		public void run() {
			while (true) {
				Command cmd = null;
				synchronized (mCmdQueue) { // 同步方式执行
					cmd = mCmdQueue.removeFirst();
				}
				switch (cmd.code) {
				case PLAY:
					startSound(cmd);
					break;
				case STOP:
					if (mPlayer != null) {
						mPlayer.stop();
						mPlayer.release();
						mPlayer = null;
					} else {
						Log.w(mTag, "STOP command without a player");
					}
					break;
				}
				synchronized (mCmdQueue) {
					if (mCmdQueue.size() == 0) {

						mThread = null;
						releaseWakeLock();
						return;
					}
				}
			}
		}
	}
	private String mTag;
	private Thread mThread;
	private MediaPlayer mPlayer;
	private PowerManager.WakeLock mWakeLock;

	private int mState = STOP;
	public AsyncPlayer(String tag) {
		if (tag != null) {
			mTag = tag;
		} else {
			mTag = "AsyncPlayer";
		}
	}
	public void play(Context context, Uri uri, boolean looping, int stream) {
		Command cmd = new Command();
		cmd.requestTime = SystemClock.uptimeMillis(); // 这里为了测试性能，传递了开始执行前的系统tickcount计时器值
		cmd.code = PLAY;
		cmd.context = context;
		cmd.uri = uri;
		cmd.looping = looping;
		cmd.stream = stream;
		synchronized (mCmdQueue) {
			enqueueLocked(cmd);
			mState = PLAY;
		}
	}
	public void stop() {
		synchronized (mCmdQueue) {
			if (mState != STOP) {
				Command cmd = new Command();
				cmd.requestTime = SystemClock.uptimeMillis();
				cmd.code = STOP;
				enqueueLocked(cmd);
				mState = STOP;
			}
		}
	}
	private void enqueueLocked(Command cmd) {
		mCmdQueue.add(cmd);
		if (mThread == null) {
			acquireWakeLock();
			mThread = new Thread();
			mThread.start();
		}
	}
	public void setUsesWakeLock(Context context) { // 电源管理wakelock处理
		if (mWakeLock != null || mThread != null) {
			throw new RuntimeException("assertion failed mWakeLock=
					+ mWakeLock + " mThread=" + mThread);
		}
		PowerManager pm = (PowerManager) context
				.getSystemService(Context.POWER_SERVICE);
		mWakeLock = pm.newWakeLock(PowerManager.PARTIAL_WAKE_LOCK, mTag);
	}
	private void acquireWakeLock() { // 加锁
		if (mWakeLock != null) {
			mWakeLock.acquire();
		}
	}
	private void releaseWakeLock() { // 解锁
		if (mWakeLock != null) {
			mWakeLock.release();
		}
	}
}
```
一般对于Android游戏而言下面的代码不用考虑，一般用户都在交互操作，不会出现屏幕锁问题。