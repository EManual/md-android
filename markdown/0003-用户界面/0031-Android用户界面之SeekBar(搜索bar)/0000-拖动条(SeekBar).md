听歌的时候，我们常常喜欢快进或者退回某一时间段，听歌的时候，我们喜欢控件音量大小来听歌。做为与用户交互密切的另外一个控件SeekBar拖动条，可以让用户拖动达到用户需要的效果的控件，在无外乎大大提高用户的体验。下面我们来讲讲此拖动条。
#### 1、拖动条的事件
由于拖动条可以被用户控制。所以需要对其进行事件监听，这就需要实现SeekBar.OnSeekBarChangeListener接口。此接口共需要监听三个事件，分别是：
```  
数值改变（onProgressChanged）
开始拖动（onStartTrackingTouch）
停止拖动（onStopTrackingTouch）
```
#### 2、拖动条的主要属性和方法
```  
setMax 
设置拖动条的数值
setProgress
设置拖动条当前的数值
setSeconddaryProgress
设置第二拖动条的数值，即当前拖动条推荐的数值
```
代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.SeekBar;
import android.widget.TextView;
import android.widget.SeekBar.OnSeekBarChangeListener;
public class SeekBarActivity extends Activity {
	private SeekBar seek;
	private TextView myTextView;
	private TextView myTextView2;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.seekbardemo);
		seek = (SeekBar) findViewById(R.id.SeekBar01);
		myTextView = (TextView) findViewById(R.id.TextView01);
		myTextView2 = (TextView) findViewById(R.id.TextView02);
		seek.setOnSeekBarChangeListener(new OnSeekBarChangeListener() {
			@Override
			public void onStopTrackingTouch(SeekBar seekBar) {
				myTextView2.setText("停止调节");
			}
			@Override
			public void onStartTrackingTouch(SeekBar seekBar) {
				myTextView2.setText("开始调节");
			}
			@Override
			public void onProgressChanged(SeekBar seekBar, int progress,
					boolean fromUser) {
				myTextView.setText("当前值 为:" + progress);
			}
		});
	}
}
```
效果图：
![img](P)  