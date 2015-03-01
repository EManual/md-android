这是启动我们自己定义的音频录制程序来完成录制工作。这个是利用MediaRecorder类来实现自己的音频录制程序。为了可以录制音频我们需要RECORD_AUDIO权限，为了可以写入SDCard，我们需要WRITE_EXTERNAL_STORAGE权限 
```  
import java.io.File;
import java.io.IOException;
import android.app.Activity;
import android.content.ContentValues;
import android.content.Intent;
import android.media.MediaPlayer;
import android.media.MediaRecorder;
import android.net.Uri;
import android.os.Bundle;
import android.os.Environment;
import android.provider.MediaStore;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
public class MyAudioRecord extends Activity {
	private TextView stateView;
	private Button btnStart, btnStop, btnPlay, btnFinish;
	private MediaRecorder recorder;
	private MediaPlayer player;
	private File audioFile;
	private Uri fileUri;
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.my_audio_record);
		stateView = (TextView) this.findViewById(R.id.view_state);
		stateView.setText("准备开始");
		btnStart = (Button) this.findViewById(R.id.btn_start);
		btnStop = (Button) this.findViewById(R.id.btn_stop);
		btnPlay = (Button) this.findViewById(R.id.btn_play);
		btnFinish = (Button) this.findViewById(R.id.btn_finish);

		btnStop.setEnabled(false);
		btnPlay.setEnabled(false);
	}
	public void onClick(View v) {
		int id = v.getId();
		switch (id) {
		case R.id.btn_start:
			// 开始录制
			// 我们需要实例化一个MediaRecorder对象，然后进行相应的设置
			recorder = new MediaRecorder();
			// 指定AudioSource 为MIC(Microphone audio source ),这是最长用的
			recorder.setAudioSource(MediaRecorder.AudioSource.MIC);
			// 指定OutputFormat,我们选择3gp格式
			// 其他格式，MPEG-4:这将指定录制的文件为mpeg-4格式，可以保护Audio和Video
			// RAW_AMR:录制原始文件，这只支持音频录制，同时要求音频编码为AMR_NB
			// THREE_GPP:录制后文件是一个3gp文件，支持音频和视频录制
			recorder.setOutputFormat(MediaRecorder.OutputFormat.THREE_GPP);
			// 指定Audio编码方式，目前只有AMR_NB格式
			recorder.setAudioEncoder(MediaRecorder.AudioEncoder.AMR_NB);
			// 接下来我们需要指定录制后文件的存储路径
			File fpath = new File(Environment.getExternalStorageDirectory()
					.getAbsolutePath() + "/data/files/");
			fpath.mkdirs();// 创建文件夹
			try {
				// 创建临时文件
				audioFile = File.createTempFile("recording", ".3gp", fpath);
			} catch (IOException e) {
				e.printStackTrace();
			}
			recorder.setOutputFile(audioFile.getAbsolutePath());
			// 下面就开始录制了
			try {
				recorder.prepare();
			} catch (IllegalStateException e) {
				e.printStackTrace();
			} catch (IOException e) {
				e.printStackTrace();
			}
			recorder.start();
			stateView.setText("正在录制");
			btnStart.setEnabled(false);
			btnPlay.setEnabled(false);
			btnStop.setEnabled(true);
			break;
		case R.id.btn_stop:
			recorder.stop();
			recorder.release();
			// 然后我们可以将我们的录制文件存储到MediaStore中
			ContentValues values = new ContentValues();
			values.put(MediaStore.Audio.Media.TITLE,
					"this is my first record-audio");
			values.put(MediaStore.Audio.Media.DATE_ADDED,
					System.currentTimeMillis());
			values.put(MediaStore.Audio.Media.DATA, audioFile.getAbsolutePath());
			fileUri = this.getContentResolver().insert(
					MediaStore.Audio.Media.EXTERNAL_CONTENT_URI, values);
			// 录制结束后，我们实例化一个MediaPlayer对象，然后准备播放
			player = new MediaPlayer();
			player.setOnCompletionListener(new MediaPlayer.OnCompletionListener() {
				@Override
				public void onCompletion(MediaPlayer arg0) {
					// 更新状态
					stateView.setText("准备录制");
					btnPlay.setEnabled(true);
					btnStart.setEnabled(true);
					btnStop.setEnabled(false);
				}
			});
			// 准备播放
			try {
				player.setDataSource(audioFile.getAbsolutePath());
				player.prepare();
			} catch (IllegalArgumentException e) {
				e.printStackTrace();
			} catch (IllegalStateException e) {
				e.printStackTrace();
			} catch (IOException e) {
				e.printStackTrace();
			}
			// 更新状态
			stateView.setText("准备播放");
			btnPlay.setEnabled(true);
			btnStart.setEnabled(true);
			btnStop.setEnabled(false);
			break;
		case R.id.btn_play:
			// 播放录音
			// 注意，我们在录音结束的时候，已经实例化了MediaPlayer，做好了播放的准备
			player.start();
			// 更新状态
			stateView.setText("正在播放");
			btnStart.setEnabled(false);
			btnStop.setEnabled(false);
			btnPlay.setEnabled(false);
			// 在播放结束的时候也要更新状态
			break;
		case R.id.btn_finish:
			// 完成录制，返回录制的音频的Uri
			Intent intent = new Intent();
			intent.setData(fileUri);
			this.setResult(RESULT_OK, intent);
			this.finish();
			break;
		}
	}
}
```