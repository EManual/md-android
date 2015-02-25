效果如下:
![img](P)  
一、与ZoomControls的区别
ZoomControls是一个包含放大、缩小按钮的控件。而ZoomButton是您自己定义的缩放按钮，它允许你定义多个这样的按钮，它显示的只能是图片，没有Text属性。
二、实例
XML布局：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="ZoomControls实例"
        android:textSize="12px" />
    <ZoomButton
        android:id="@+id/zoombutton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:src="@drawable/btn_black" />
</LinearLayout>
```
代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.Vie.OnClickListener;
import android.widget.TextView;
import android.widget.ZoomButton;
import android.widget.ZoomButtonsController;
import android.widget.ZoomControls;
public class ZoomButtonsControllerDemo extends Activity {
	private ZoomButton zb;
	private TextView text;
	static long size = 12;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.zoombuttonscontroller);
		zb = (ZoomButton) findViewById(R.id.zoombutton);
		text = (TextView) findViewById(R.id.text);
		zb.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				size = size + 2;
				text.setTextSize(size);
			}
		});
	}
}
```