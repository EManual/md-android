最简单的使用Drawable资源的方法是，把图片放入Android工程的res\drawable目录下，编程环境会自动在R类里为此资源创建一个引用。
```  
<?xml version="1.0" encoding="utf-8"?>
<LINEARLAYOUT xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TEXTVIEW
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Drawable的使用-设置壁纸"
        android:textsize="20sp" />
    <BUTTON
        android:id="@+id/Button01"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="查看图片A"
        android:textsize="20sp" >
    </BUTTON>
    <BUTTON
        android:id="@+id/Button02"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="查看图片B"
        android:textsize="20sp" >
    </BUTTON>
    <BUTTON
        android:id="@+id/Button03"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="设置图片A为壁纸"
        android:textsize="20sp" >
    </BUTTON>
    <BUTTON
        android:id="@+id/Button04"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="设置图片B为壁纸"
        android:textsize="20sp" >
    </BUTTON>
    <BUTTON
        android:id="@+id/Button05"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="恢复默认壁纸"
        android:textsize="20sp" >
    </BUTTON>
    <IMAGEVIEW
        android:id="@+id/ImageView01"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" >
    </IMAGEVIEW>
</LINEARLAYOUT>
```
代码如下：
```  
import java.io.IOException;
import android.app.Activity;
import android.graphics.BitmapFactory;
import android.graphics.drawable.Drawable;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.ImageView;
public class MainDrawable extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 定义UI组件
		Button b1 = (Button) findViewById(R.id.Button01);
		Button b2 = (Button) findViewById(R.id.Button02);
		Button b3 = (Button) findViewById(R.id.Button03);
		Button b4 = (Button) findViewById(R.id.Button04);
		Button b5 = (Button) findViewById(R.id.Button05);
		final ImageView iv = (ImageView) findViewById(R.id.ImageView01);
		// 定义按钮点击监听器
		OnClickListener ocl = new OnClickListener() {
			@Override
			public void onClick(View v) {
				switch (v.getId()) {
				case R.id.Button01:
					// 给ImageView设置图片，从存储卡中获取图片为Drawable,然后把Drawable设置为ImageView的背景
					iv.setBackgroundDrawable(Drawable
							.createFromPath("/sdcard/a.jpg"));
					break;
				case R.id.Button02:
					iv.setBackgroundDrawable(Drawable
							.createFromPath("/sdcard/b.jpg"));
					break;
				case R.id.Button03:
					try {
						// Activity的父类ContextWrapper有这个setWallpaper方法，当然使用此方法需要有android.permission.SET_WALLPAPER权限
						setWallpaper(BitmapFactory.decodeFile("/sdcard/a.jpg"));
					} catch (IOException e1) {
						e1.printStackTrace();
					}
					break;
				case R.id.Button04:
					try {
						setWallpaper(BitmapFactory.decodeFile("/sdcard/b.jpg"));
					} catch (IOException e1) {
						e1.printStackTrace();
					}
					break;
				case R.id.Button05:
					try {
						// Activity的父类ContextWrapper有这个clearWallpaper方法，作用是恢复默认壁纸，当然使用此方法需要有android.permission.SET_WALLPAPER权限
						clearWallpaper();
					} catch (IOException e) {
						e.printStackTrace();
					}
					break;
				}
			}
		};
		// 给按钮们绑定点击监听器
		b1.setOnClickListener(ocl);
		b2.setOnClickListener(ocl);
		b3.setOnClickListener(ocl);
		b4.setOnClickListener(ocl);
		b5.setOnClickListener(ocl);
	}
}
```
AndroidManifest.xml的内容如下（设置权限）：
```  
<?xml version="1.0" encoding="utf-8"?>
<MANIFEST xmlns:android="http://schemas.android.com/apk/res/android"
    package="android.basic.lesson23"
    android:versioncode="1"
    android:versionname="1.0" >
    <APPLICATION
        android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <ACTIVITY
            android:name=".MainDrawable"
            android:label="@string/app_name" >
            <INTENT filter="" >
                <ACTION android:name="android.intent.action.MAIN" />
                <CATEGORY android:name="android.intent.category.LAUNCHER" />
            </INTENT>
        </ACTIVITY>
    </APPLICATION>
    <USES
        sdk=""
        android:minsdkversion="8" />
    <USES
        android:name="android.permission.SET_WALLPAPER"
        permission="" >
    </USES>
</MANIFEST>
```
效果图：
点击“查看图片A”按钮，ImageView载入图片A并显示出来
![img](P)  
点击”设置图片B为壁纸”按钮，可以看到图片B已经成为桌面壁纸
![img](P)  