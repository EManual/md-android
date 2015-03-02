在做Setting界面时，用Android自带的 PreferenceScreen 时 发现 没有提供 SeekBarPreference（在Android的源代码中有这么一个 SeekBarPreference 但是没有写完，所以到Android2.1为止还不提供这个功能） 所以只能自己重写一个可以满足需求的 SeekBarPreference 代码如下：
```  
import android.app.AlertDialog.Builder;
import android.content.Context;
import android.preference.DialogPreference;
import android.util.AttributeSet;
import android.view.ViewGroup;
import android.widget.LinearLayout;
import android.widget.SeekBar;
public class SeekBarPreference extends DialogPreference {
	private Context context;
	private SeekBar sensitivityLevel = null;
	private LinearLayout layout = null;
	public SeekBarPreferenceCust(Context context, AttributeSet attrs) {
		super(context, attrs);
		this.context = context;
		persistInt(10);
	}
	protected void onPrepareDialogBuilder(Builder builder) {
		// 添加布局
		layout = new LinearLayout(context);
		layout.setLayoutParams(new LinearLayout.LayoutParams(
				LinearLayout.LayoutParams.FILL_PARENT,
				LinearLayout.LayoutParams.WRAP_CONTENT)); // 布局属性

		layout.setMinimumWidth(400); // 布局的最小宽度

		layout.setPadding(20, 20, 20, 20); // 上下左右的Padding

		// 添加SeekBar
		sensitivityLevel = new SeekBar(context);

		sensitivityLevel.setMax(100); // 最大值

		sensitivityLevel.setLayoutParams(new ViewGroup.LayoutParams(
				ViewGroup.LayoutParams.FILL_PARENT,
				ViewGroup.LayoutParams.WRAP_CONTENT)); // SeekBar的布局属性
		sensitivityLevel.setProgress(getPersistedInt(10)); // 设置默认值
		layout.addView(sensitivityLevel); // 把SeekBar加到 layout的布局中
		builder.setView(layout);
	}
	protected void onDialogClosed(boolean positiveResult) {
		if (positiveResult) {
			persistInt(sensitivityLevel.getProgress()); // 保存SeekBar的值
		}
		super.onDialogClosed(positiveResult);
	}
} 
```
使用方式：在 Res -》 Xml 中添加一个 preference.xml 也可以加到Layout中
preference.xml 内容如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<referenceScreen xmlns:android="http://schemas.android.com/apk/res/android">
    <!--以上自定义的 SeekBarPreference -->
    <asai.cn.seekbardemo.SeekBarPreference
        <!-- SeekBarPreference 的key 外部是跟这个Key来取得配置的值的-->
        android:key="CustSeekBar"
        <!-- SeekBarPreference 在List中显示的标题-->
        android:title="Custom Seek Bar"
        <!--SeekBarPreference 弹出对话框的 标题 -->
		android:dialogTitle="Custom Seek Bar"
        android:persistent="true" />
</PreferenceScreen>
```
添加好preference.xml后就可以在主界面中使用了：
```  
import android.os.Bundle;
import android.preference.PreferenceActivity;
public class seekBarDemo extends PreferenceActivity {
	 /**
	  * Called when the activity is first created.
	  */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		addPreferencesFromResource(R.xml.preference);
	}
}
```
运行的结果参看：

![img](http://emanual.github.io/md-android/img/data_preference/21_preference.jpg)