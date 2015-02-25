一下就是给大家分享的录制视频的例子，当中我会把注释写在代码里，在这里我也就不多说了。
我们还是先来看看主要的代码：
```  
import java.io.File;
import java.io.IOException;
import android.app.Activity;
import android.media.MediaRecorder;
import android.os.Bundle;
import android.os.Environment;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
public class VideoActivity extends Activity {
	private File myRecAudioFile;
	private SurfaceView mSurfaceView;
	private SurfaceHolder mSurfaceHolder;
	private Button buttonStart;
	private Button buttonStop;
	private File dir;
	private MediaRecorder recorder;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.video);
		mSurfaceView = (SurfaceView) findViewById(R.id.videoView);
		mSurfaceHolder = mSurfaceView.getHolder();
		mSurfaceHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
		buttonStart = (Button) findViewById(R.id.start);
		buttonStop = (Button) findViewById(R.id.stop);
		File defaultDir = Environment.getExternalStorageDirectory();
		String path = defaultDir.getAbsolutePath() + File.separator + "V
				+ File.separator;
		// 创建文件夹存放视频
		dir = new File(path);
		if (!dir.exists()) {
			dir.mkdir();
		}
		recorder = new MediaRecorder();
		buttonStart.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				recorder();
			}
		});
		buttonStop.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				recorder.stop();
				recorder.reset();
				recorder.release();
				recorder = null;
			}
		});
	}
	public void recorder() {
		try {
			myRecAudioFile = File.createTempFile("video", ".3gp", dir);// 创建临时文件
			recorder.setPreviewDisplay(mSurfaceHolder.getSurface());// 预览
			recorder.setVideoSource(MediaRecorder.VideoSource.CAMERA);// 视频源
			recorder.setAudioSource(MediaRecorder.AudioSource.MIC); // 录音源为麦克风
			recorder.setOutputFormat(MediaRecorder.OutputFormat.THREE_GPP);// 输出格式为3gp
			recorder.setVideoSize(800, 480);// 视频尺寸
			recorder.setVideoFrameRate(15);// 视频帧频率
			recorder.setVideoEncoder(MediaRecorder.VideoEncoder.H263);// 视频编码
			recorder.setAudioEncoder(MediaRecorder.AudioEncoder.AMR_NB);// 音频编码
			recorder.setMaxDuration(10000);// 最大期限
			recorder.setOutputFile(myRecAudioFile.getAbsolutePath());// 保存路径
			recorder.prepare();
			recorder.start();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```
我们现在就来看看布局的代码，其实在一个程序当中布局是很关键的，希望大家要多多的有自己的思想在里面，别老是用死板的界面：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <SurfaceView
        android:id="@+id/videoView"
        android:layout_width="320px"
        android:layout_height="240px"
        android:visibility="visible" >
    </SurfaceView>
    <RelativeLayout
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" >
        <Button
            android:id="@+id/start"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="录制" />
        <Button
            android:id="@+id/stop"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_toRightOf="@id/start"
            android:text="停止" />
    </RelativeLayout>
</LinearLayout>
```
最后我们就来看看AndroidManifest.xml代码：
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="eoe.demo.Media"
    android:versionCode="1"
    android:versionName="1.0" >
    <application
        android:icon="@drawable/rabbit"
        android:label="@string/app_name" >
        <activity
            android:name=".VideoActivity"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-sdk android:minSdkVersion="7" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
</manifest>
```