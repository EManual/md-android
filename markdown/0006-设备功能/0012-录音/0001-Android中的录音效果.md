其实我们要是想实现录音效果的话，大脑里第一个想的就是先把布局写好，完事以后我们在设置录音按钮点击事件。完事创建录音对象，我们还应该想到的就是设置输出格式、设置编码格式、设置输出文件。这些主要的设置完，我们的录音代码就快完成，剩下的就是一些不怎么主要的了，记住还要设置权限。下面我们就来看看代码是怎么写的吧：
```  
<?xml version="1.0" encoding="utf-8"?>
<LINEARLAYOUT xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical"
    android:gravity="center" >
    <BUTTON
        android:id="@+id/Button01"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="录音"
        android:textsize="30sp" >
    </BUTTON>
    <BUTTON
        android:id="@+id/Button02"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margintop="20dp"
        android:text="停止"
        android:textsize="30sp" >
    </BUTTON>
</LINEARLAYOUT>
```
下面是main代码：
```  
import java.io.File;
import java.io.IOException;
import java.util.Calendar;
import java.util.Locale;
import android.app.Activity;
import android.media.MediaRecorder;
import android.os.Bundle;
import android.text.format.DateFormat;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;
public class MainActivity extends Activity {
	private Button recordButton;
	private Button stopButton;
	private MediaRecorder mr;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		recordButton = (Button) this.findViewById(R.id.Button01);
		stopButton = (Button) this.findViewById(R.id.Button02);
		// 录音按钮点击事件
		recordButton.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				File file = new File("/sdcard/
						+ "YY
						+ new DateFormat().format("yyyyMMdd_hhmmss",
								Calendar.getInstance(Locale.CHINA)) + ".amr");
				Toast.makeText(getApplicationContext(),
						"正在录音，录音文件在" + file.getAbsolutePath(),
						Toast.LENGTH_LONG).show();
				// 创建录音对象
				mr = new MediaRecorder();
				// 从麦克风源进行录音
				mr.setAudioSource(MediaRecorder.AudioSource.DEFAULT);
				// 设置输出格式
				mr.setOutputFormat(MediaRecorder.OutputFormat.DEFAULT);
				// 设置编码格式
				mr.setAudioEncoder(MediaRecorder.AudioEncoder.DEFAULT);
				// 设置输出文件
				mr.setOutputFile(file.getAbsolutePath());
				try {
					// 创建文件
					file.createNewFile();
					// 准备录制
					mr.prepare();
				} catch (IllegalStateException e) {
					e.printStackTrace();
				} catch (IOException e) {
					e.printStackTrace();
				}
				// 开始录制
				mr.start();
				recordButton.setText("录音中……");
			}
		});
		// 停止按钮点击事件
		stopButton.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				if (mr != null) {
					mr.stop();
					mr.release();
					mr = null;
					recordButton.setText("录音");
					Toast.makeText(getApplicationContext(), "录音完毕",
							Toast.LENGTH_LONG).show();
				}
			}
		});
	}
}
```
下面就是很重要的了，就是在AndroidManifest.xml里设置权限，不写它你就无法实现效果。
```  
<?xml version="1.0" encoding="utf-8"?>
<MANIFEST xmlns:android="http://schemas.android.com/apk/res/android"
    android:versioncode="1"
    android:versionname="1.0" >
    <APPLICATION
        android:debuggable="true"
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <ACTIVITY
            android:name=".MainActivity"
            android:configchanges="orientation|keyboardHidden|keyboard"
            android:label="@string/app_name"
            android:screenorientation="portrait" >
            <INTENT filter="" >
                <ACTION android:name="android.intent.action.MAIN" />
                <CATEGORY android:name="android.intent.category.LAUNCHER" />
            </INTENT>
        </ACTIVITY>
    </APPLICATION>
    <USES
        sdk=""
        android:minsdkversion="4" />
    <USES
        android:name="android.permission.RECORD_AUDIO"
        permission="" >
    </USES>
    <USES
        android:name="android.permission.WRITE_EXTERNAL_STORAGE"
        permission="" >
    </USES>
</MANIFEST>
```
效果图：

![img](http://emanual.github.io/md-android/img/device_record/02_record.jpg) 

当点击录音时

![img](http://emanual.github.io/md-android/img/device_record/02_record2.jpg) 