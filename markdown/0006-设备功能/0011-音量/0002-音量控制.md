代码如下：
```  
import android.app.Activity;
import android.content.Context;
import android.media.AudioManager;
import android.media.SoundPool;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.view.Window;
import android.view.View.OnClickListener;
import android.widget.TextView;
public class VolumeControl extends Activity {
	private AudioManager audioManager;
	private SoundPool spool;
	private int hit;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		setTitle("点击play播放，使用音量加减键来调节音量！");
		final TextView volumenum = (TextView) findViewById(R.id.volumenum);
		// 创建对象
		// 第一个参数指定音频池的最大音频流数目为10
		// 第三个参数，声音品质为5
		spool = new SoundPool(10, AudioManager.STREAM_SYSTEM, 10);
		// 从资源或者文件截入音频流
		hit = spool.load(this, R.raw.msg, 0);
		volumenum.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				// 播放音频，可以对左右音量分别设置，可以设置优先级，循环次数以及速率
				// 速率最高2，最低0.5，正常为1
				float volumeNum = (float) getVolume() / 7;
				int streamID = spool.play(hit, 1, 1, 0, 0, (float) 1.4);
				spool.setVolume(streamID, volumeNum, volumeNum);

			}
		});
	}
	// 获得当前系统音量 0~7
	private int getVolume() {
		int volume = -1;
		audioManager = (AudioManager) getSystemService(Context.AUDIO_SERVICE);
		volume = audioManager.getStreamVolume(AudioManager.STREAM_RING);
		Log.i("STREAM_RING", "" + volume);
		return volume;
	}
}
```