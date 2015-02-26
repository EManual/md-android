直接给上个详细的解说代码：
```  
import java.io.IOException;
import android.app.Activity;
import android.media.MediaRecorder;
import android.os.Bundle;
 /**
  * @description 对通过android系统手机进行录音的一点说明测试
  */
public class MediaRecordActivity extends Activity {
	MediaRecorder mediaRecorder;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		mediaRecorder = new MediaRecorder();
		record();
	}
	 /**
	  * 开始录制
	  */
	private void record() {
		 /**
		  * mediaRecorder.setAudioSource设置声音来源。
		  * MediaRecorder.AudioSource这个内部类详细的介绍了声音来源。
		  * 该类中有许多音频来源，不过最主要使用的还是手机上的麦克风，MediaRecorder.AudioSource.MIC
		  */
		mediaRecorder.setAudioSource(MediaRecorder.AudioSource.MIC);
		 /**
		  * mediaRecorder.setOutputFormat代表输出文件的格式。该语句必须在setAudioSource之后，
		  * 在prepare之前。
		  * OutputFormat内部类，定义了音频输出的格式，主要包含MPEG_4、THREE_GPP、RAW_AMR……等。
		  */
		mediaRecorder.setOutputFormat(MediaRecorder.OutputFormat.THREE_GPP);
		 /**
		  * mediaRecorder.setAddioEncoder()方法可以设置音频的编码
		  * AudioEncoder内部类详细定义了两种编码：AudioEncoder.DEFAULT、AudioEncoder.AMR_NB
		  */
		mediaRecorder.setAudioEncoder(MediaRecorder.AudioEncoder.DEFAULT);
		 /**
		  * 设置录音之后，保存音频文件的位置
		  */
		mediaRecorder.setOutputFile("file:///sdcard/myvido/a.3pg");
		 /**
		  * 调用start开始录音之前，一定要调用prepare方法。
		  */
		try {
			mediaRecorder.prepare();
			mediaRecorder.start();
		} catch (IllegalStateException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	 /***
	  * 此外，还有和MediaRecorder有关的几个参数与方法，我们一起来看一下： sampleRateInHz
	  * :音频的采样频率，每秒钟能够采样的次数，采样率越高，音质越高。
	  * 给出的实例是44100、22050、11025但不限于这几个参数。例如要采集低质量的音频就可以使用4000、8000等低采样率
	  * 
	  * channelConfig ：声道设置：android支持双声道立体声和单声道。MONO单声道，STEREO立体声
	  * 
	  * recorder.stop();停止录音 recorder.reset(); 重置录音 ，会重置到setAudioSource这一步
	  * recorder.release(); 解除对录音资源的占用
	  */
}
```
这里，一定要注意一点，那就是如果我们想要录音的话，那么首先得添加录音权限到AndroidManiferst.xml中：
```  
<uses-permission android:name="android.permission.RECORD_AUDIO"></uses-permission>
```