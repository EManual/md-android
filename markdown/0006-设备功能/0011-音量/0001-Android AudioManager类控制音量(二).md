#### 2、AudioManagerActivity
程序入口处，和主要逻辑代码的处理，程序开头以(AudioManager)getSystemService(AUDIO_SERVICE);取得AudioManager对象。然后再利用该对象来对铃声进行调整。声明了一个返回 ImageButton 的方法，用来处理各自按钮点击所执行的不同事件和对图片的状态进行调整设置。以下把代码提供给大家：
```  
import android.app.Activity;
import android.media.AudioManager;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.ImageButton;
import android.widget.ImageView;
import android.widget.ProgressBar;
public class AudioManagerActivity extends Activity {
	// 音量变量
	private int volume = 0;
	// 声音模式
	private int mode;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		findview();
		// 通过getStreamVolume 获得当前音量大小
		volume = viewHolder.audiomanage
				.getStreamVolume(AudioManager.STREAM_RING);
		// 把当前音量的值 设置给进度条
		viewHolder.myProgressBar.setProgress(volume);
		// 得到当前的声音模式
		mode = viewHolder.audiomanage.getRingerMode();
		setImageState();
		viewHolder.downButton = btnListener(viewHolder.downButton);
		viewHolder.upButton = btnListener(viewHolder.upButton);
		viewHolder.muteButton = btnListener(viewHolder.muteButton);
		viewHolder.normalButton = btnListener(viewHolder.normalButton);
		viewHolder.vibrateButton = btnListener(viewHolder.vibrateButton);
	}
	// 找到控件
	void findview() {
		viewHolder.downButton = (ImageButton) findViewById(R.id.downButton);
		viewHolder.upButton = (ImageButton) findViewById(R.id.upButton);
		viewHolder.muteButton = (ImageButton) findViewById(R.id.muteButton);
		viewHolder.normalButton = (ImageButton) findViewById(R.id.normalButton);
		viewHolder.vibrateButton = (ImageButton) findViewById(R.id.vibrateButton);
		viewHolder.myImageView = (ImageView) findViewById(R.id.myImage);
		viewHolder.myProgressBar = (ProgressBar) findViewById(R.id.myProgress);
		viewHolder.audiomanage = (AudioManager) getSystemService(AUDIO_SERVICE);
	}
	// 按钮 的单击事件
	ImageButton btnListener(ImageButton b) {
		b.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				switch (v.getId()) {
				case R.id.downButton:
					viewHolder.audiomanage.adjustVolume(
							AudioManager.ADJUST_LOWER, 0);
					volume = viewHolder.audiomanage
							.getStreamVolume(AudioManager.STREAM_RING);
					viewHolder.myProgressBar.setProgress(volume);
					mode = viewHolder.audiomanage.getRingerMode();
					setImageState();
					break;
				case R.id.upButton:
					viewHolder.audiomanage.adjustVolume(
							AudioManager.ADJUST_RAISE, 0);
					volume = viewHolder.audiomanage
							.getStreamVolume(AudioManager.STREAM_RING);
					viewHolder.myProgressBar.setProgress(volume);
					mode = viewHolder.audiomanage.getRingerMode();
					setImageState();
					break;
				case R.id.muteButton:
					viewHolder.audiomanage
							.setRingerMode(AudioManager.RINGER_MODE_SILENT);
					volume = viewHolder.audiomanage
							.getStreamVolume(AudioManager.STREAM_RING);
					viewHolder.myProgressBar.setProgress(volume);
					viewHolder.myImageView.setImageDrawable(getResources()
							.getDrawable(R.drawable.mute));
					break;
				case R.id.normalButton:
					viewHolder.audiomanage
							.setRingerMode(AudioManager.RINGER_MODE_NORMAL);
					volume = viewHolder.audiomanage
							.getStreamVolume(AudioManager.STREAM_RING);
					viewHolder.myProgressBar.setProgress(volume);
					viewHolder.myImageView.setImageDrawable(getResources()
							.getDrawable(R.drawable.normal));
					break;
				case R.id.vibrateButton:
					viewHolder.audiomanage
							.setRingerMode(AudioManager.RINGER_MODE_VIBRATE);
					volume = viewHolder.audiomanage
							.getStreamVolume(AudioManager.STREAM_RING);
					viewHolder.myProgressBar.setProgress(volume);
					viewHolder.myImageView.setImageDrawable(getResources()
							.getDrawable(R.drawable.vibrate));
					break;
				}
			}
		});
		return b;
	}
	// 设置图片状态
	void setImageState() {
		if (mode == AudioManager.RINGER_MODE_NORMAL) {
			viewHolder.myImageView.setImageDrawable(getResources().getDrawable(
					R.drawable.normal));
		} else if (mode == AudioManager.RINGER_MODE_SILENT) {
			viewHolder.myImageView.setImageDrawable(getResources().getDrawable(
					R.drawable.mute));
		} else if (mode == AudioManager.RINGER_MODE_VIBRATE) {
			viewHolder.myImageView.setImageDrawable(getResources().getDrawable(
					R.drawable.vibrate));
		}
	}
}
```