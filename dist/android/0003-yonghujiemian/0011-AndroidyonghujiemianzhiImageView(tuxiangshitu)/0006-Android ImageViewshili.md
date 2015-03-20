ImageView控件是一个图片控件，负责显示图片。
以下模拟手机图片查看器
目录结构
![img](http://emanual.github.io/md-android/img/view_imageview/07_imageview.png) 
main.xml布局文件
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <ImageView
        android:id="@+id/imageView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:src="@drawable/p1" />
    <LinearLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:orientation="horizontal" >
        <Button
            android:id="@+id/previous"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:text="上一张" />
        <Button
            android:id="@+id/alpha_plus"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:text="透明度增加" />
        <Button
            android:id="@+id/alpha_minus"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:text="透明度减少" />
        <Button
            android:id="@+id/next"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:text="下一张" />
    </LinearLayout>
</LinearLayout>
```
ImageViewActivity类
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.ImageView;
public class ImageViewActivity extends Activity {
	private ImageView imageView = null;
	private Button previous = null;// 上一张
	private Button next = null;// 下一张
	private Button alpha_plus = null;// 透明度增加
	private Button alpha_minus = null;// 透明度减少
	private int currentImgId = 0;// 记录当前ImageView显示的图片id
	private int alpha = 255;// 记录ImageView的透明度
	int[] imgId = { // ImageView显示的图片数组
	R.drawable.p1, R.drawable.p2, R.drawable.p3, R.drawable.p4, R.drawable.p5,
			R.drawable.p6, R.drawable.p7, R.drawable.p8, };
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		imageView = (ImageView) findViewById(R.id.imageView);
		previous = (Button) findViewById(R.id.previous);
		next = (Button) findViewById(R.id.next);
		alpha_plus = (Button) findViewById(R.id.alpha_plus);
		alpha_minus = (Button) findViewById(R.id.alpha_minus);
		previous.setOnClickListener(listener);
		next.setOnClickListener(listener);
		alpha_plus.setOnClickListener(listener);
		alpha_minus.setOnClickListener(listener);
	}
	private View.OnClickListener listener = new View.OnClickListener() {
		public void onClick(View v) {
			if (v == previous) {
				currentImgId = (currentImgId - 1 + imgId.length) % imgId.length;
				imageView.setImageResource(imgId[currentImgId]);
			}
			if (v == next) {
				currentImgId = (currentImgId + 1) % imgId.length;
				imageView.setImageResource(imgId[currentImgId]);
			}
			if (v == alpha_plus) {
				alpha += 10;
				if (alpha > 255) {
					alpha = 255;
				}
				imageView.setAlpha(alpha);
			}
			if (v == alpha_minus) {
				alpha -= 10;
				if (alpha < 0) {
					alpha = 0;
				}
				imageView.setAlpha(alpha);
			}
		}
	};
}
```
![img](http://emanual.github.io/md-android/img/view_imageview/07_imageview2.jpg) 