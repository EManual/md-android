今天实现了下屏幕截图 但是方法不是很优雅 还请大牛给予指点
界面很简单:
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/rootLayout"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello" />
    <Button
        android:id="@+id/btn"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="save" />
</LinearLayout>
```
界面视图:

![img](http://emanual.github.io/md-android/img/media_screen/09_screen.jpg)   

下面是代码:
```  
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import android.app.Activity;
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.Canvas;
import android.graphics.Bitmap.Config;
import android.os.Bundle;
import android.os.Environment;
import android.view.View;
import android.widget.Button;
public class Main extends Activity {
	Button btn;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		btn = (Button) findViewById(R.id.btn);
		btn.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				Context context = getApplicationContext();
				View rootView = findViewById(R.id.rootLayout);
				Bitmap newb = Bitmap.createBitmap(320, 480, Config.ARGB_8888);
				Canvas canvas = new Canvas(newb);
				rootView.draw(canvas);
				File file = new File(Environment.getExternalStorageDirectory()
						+ "/" + "1.png");
				FileOutputStream f = null;
				try {
					f = new FileOutputStream(file);
				} catch (FileNotFoundException e) {
					e.printStackTrace();
				}
				boolean b = newb.compress(Bitmap.CompressFormat.PNG, 100, f);

				if (b) {
					// 截图成功
				}
			}
		});
	}
}
```
成功后保存到SD卡中 效果如下:
![img](http://emanual.github.io/md-android/img/media_screen/09_screen2.jpg)   