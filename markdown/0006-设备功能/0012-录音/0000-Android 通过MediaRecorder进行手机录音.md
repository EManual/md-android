直接给上个详细的解说代码：
```  
import java.io.IOException;
import android.app.Activity;
import android.media.MediaRecorder;
import android.os.Bundle;
[Tags]/**
 [Tags]* @description 对通过android系统手机进行录音的一点说明测试
 [Tags]*/
public class MediaRecordActivity extends Activity {
	MediaRecorder mediaRecorder;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		mediaRecorder = new MediaRecorder();
		record();
	}
	[Tags]/**
	 [Tags]* 开始录制
	 [Tags]*/
	private void record() {
		[Tags]/**
		 [Tags]* mediaRecorder.setAudioSource设置声音来源。
		 [Tags]* MediaRecorder.AudioSource这个内部类详细的介绍了声音来源。
		 [Tags]* 该类中有许多音频来源，不过最主要使用的还是手机上的麦克风，MediaRecorder.AudioSource.MIC
		 [Tags]*/
		mediaRecorder.setAudioSource(MediaRecorder.AudioSource.MIC);
		[Tags]/**
		 [Tags]* mediaRecorder.setOutputFormat代表输出文件的格式。该语句必须在setAudioSource之后，
		 [Tags]* 在prepare之前。
		 [Tags]* OutputFormat内部类，定义了音频输出的格式，主要包含MPEG_4、THREE_GPP、RAW_AMR……等。
		 [Tags]*/
		mediaRecorder.setOutputFormat(MediaRecorder.OutputFormat.THREE_GPP);
		[Tags]/**
		 [Tags]* mediaRecorder.setAddioEncoder()方法可以设置音频的编码
		 [Tags]* AudioEncoder内部类详细定义了两种编码：AudioEncoder.DEFAULT、AudioEncoder.AMR_NB
		 [Tags]*/
		mediaRecorder.setAudioEncoder(MediaRecorder.AudioEncoder.DEFAULT);
		[Tags]/**
		 [Tags]* 设置录音之后，保存音频文件的位置
		 [Tags]*/
		mediaRecorder.setOutputFile("file:///sdcard/myvido/a.3pg");
		[Tags]/**
		 [Tags]* 调用start开始录音之前，一定要调用prepare方法。
		 [Tags]*/
		try {
			mediaRecorder.prepare();
			mediaRecorder.start();
		} catch (IllegalStateException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	[Tags]/***
	 [Tags]* 此外，还有和MediaRecorder有关的几个参数与方法，我们一起来看一下： sampleRateInHz
	 [Tags]* :音频的采样频率，每秒钟能够采样的次数，采样率越高，音质越高。
	 [Tags]* 给出的实例是44100、22050、11025但不限于这几个参数。例如要采集低质量的音频就可以使用4000、8000等低采样率
	 [Tags]* 
	 [Tags]* channelConfig ：声道设置：android支持双声道立体声和单声道。MONO单声道，STEREO立体声
	 [Tags]* 
	 [Tags]* recorder.stop();停止录音 recorder.reset(); 重置录音 ，会重置到setAudioSource这一步
	 [Tags]* recorder.release(); 解除对录音资源的占用
	 [Tags]*/
}
```
这里，一定要注意一点，那就是如果我们想要录音的话，那么首先得添加录音权限到AndroidManiferst.xml中：
```  
<uses-permission android:name="android.permission.RECORD_AUDIO"></uses-permission>
```