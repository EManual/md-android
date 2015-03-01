今天要和大家介绍MediaPlayer的使用。MediaPlayer可以播放音频和视频，另外也可以通过VideoView来播放视频，虽然VideoView比MediaPlayer简单易用，但定制性不如用 MediaPlayer，要视情况选择了。MediaPlayer 播放音频比较简单，但是要播放视频就需要SurfaceView。SurfaceView 比普通的自定义 View 更有绘图上的优势，它支持完全的 OpenGL ES 库。
```  
import java.io.IOException;
import java.util.Timer;
import java.util.TimerTask;
import android.app.Activity;
import android.media.AudioManager;
import android.media.MediaPlayer;
import android.os.Bundle;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.View;
import android.widget.Button;
import android.widget.SeekBar;
import android.widget.Toast;
public class testMedia extends Activity {
	 /** Called when the activity is first created. */
	private SeekBar skb_audio = null;
	private Button btn_start_audio = null;
	private Button btn_stop_audio = null;
	private SeekBar skb_video = null;
	private Button btn_start_video = null;
	private Button btn_stop_video = null;
	private SurfaceView surfaceView;
	private SurfaceHolder surfaceHolder;
	private MediaPlayer m = null;
	private Timer mTimer;
	private TimerTask mTimerTask;
	private boolean isChanging = false;
	// 互斥变量，防止定时器与SeekBar 拖动时进度冲突
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// ‐‐‐‐‐‐‐‐‐‐Media 控件设置‐‐‐‐‐‐‐‐‐//
		m = new MediaPlayer();
		// 播放结束之后弹出提示
		m.setOnCompletionListener(new MediaPlayer.OnCompletionListener() {
			@Override
			public void onCompletion(MediaPlayer arg0) {
				Toast.makeText(testMedia.this, "结束", 1000).show();
				m.release();
			}
		});
		// ‐‐‐‐‐‐‐‐‐‐定时器记录播放进度‐‐‐‐‐‐‐‐‐//
		mTimer = new Timer();
		mTimerTask = new TimerTask() {
			@Override
			public void run() {
				if (isChanging == true)
					return;
				if (m.getVideoHeight() == 0)
					skb_audio.setProgress(m.getCurrentPosition());
				else
					skb_video.setProgress(m.getCurrentPosition());
			}
		};
		mTimer.schedule(mTimerTask, 0, 10);
		btn_start_audio = (Button) this.findViewById(R.id.Button01);
		btn_stop_audio = (Button) this.findViewById(R.id.Button02);
		btn_start_audio.setOnClickListener(new ClickEvent());
		btn_stop_audio.setOnClickListener(new ClickEvent());
		skb_audio = (SeekBar) this.findViewById(R.id.SeekBar01);
		skb_audio.setOnSeekBarChangeListener(new SeekBarChangeEvent());
		btn_start_video = (Button) this.findViewById(R.id.Button03);
		btn_stop_video = (Button) this.findViewById(R.id.Button04);
		btn_start_video.setOnClickListener(new ClickEvent());
		btn_stop_video.setOnClickListener(new ClickEvent());
		skb_video = (SeekBar) this.findViewById(R.id.SeekBar02);
		skb_video.setOnSeekBarChangeListener(new SeekBarChangeEvent());
		surfaceView = (SurfaceView) findViewById(R.id.SurfaceView01);
		surfaceHolder = surfaceView.getHolder();
		surfaceHolder.setFixedSize(100, 100);
		surfaceHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
	}
	/*
	  * 按键事件处理
	  */
	class ClickEvent implements View.OnClickListener {

		@Override
		public void onClick(View v) {
			if (v == btn_start_audio) {
				m.reset();// 恢复到未初始化的状态
				m = MediaPlayer.create(testMedia.this, R.raw.big);// 读取音频
				skb_audio.setMax(m.getDuration());// 设置SeekBar 的长度
				try {
					m.prepare(); // 准备
				} catch (IllegalStateException e) {
					// TODO Auto‐generated catch block
					e.printStackTrace();
				} catch (IOException e) {
					// TODO Auto‐generated catch block
					e.printStackTrace();
				}
				m.start(); // 播放
			} else if (v == btn_stop_audio || v == btn_stop_video) {
				m.stop();
			} else if (v == btn_start_video) {
				m.reset();// 恢复到未初始化的状态
				m = MediaPlayer.create(testMedia.this, R.raw.test);// 读取视频
				skb_video.setMax(m.getDuration());// 设置SeekBar 的长度
				m.setAudioStreamType(AudioManager.STREAM_MUSIC);
				m.setDisplay(surfaceHolder);// 设置屏幕
				try {
					m.prepare();
				} catch (IllegalArgumentException e) {
					e.printStackTrace();
				} catch (IllegalStateException e) {
					e.printStackTrace();
				} catch (IOException e) {
					e.printStackTrace();
				}
				m.start();
			}
		}
	}
	/*
	  * SeekBar 进度改变事件
	  */
	class SeekBarChangeEvent implements SeekBar.OnSeekBarChangeListener {
		@Override
		public void onProgressChanged(SeekBar seekBar, int progress,
				boolean fromUser) {
		}
		@Override
		public void onStartTrackingTouch(SeekBar seekBar) {
			isChanging = true;
		}
		@Override
		public void onStopTrackingTouch(SeekBar seekBar) {
			m.seekTo(seekBar.getProgress());
			isChanging = false;
		}
	}
}
```