#### 1.Activity全透明
先在res/values下建colors.xml文件，写入：
```  
<?xml version = "1.0" encoding="UTF-8"?>
<resources>
	<color name="transparent"> 9000 </color>
</resources>
```
这个值设定了整个界面的透明度，为了看得见效果，现在设为透明度为56%(9/16)左右。
再在res/values/下建styles.xml,设置程序的风格
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<style name="Transparent">
		<item name="Android:windowBackground">@color/transparent</item>
		<item name="android:windowIsTranslucent">true</item>
		<item name="android:windowAnimationStyle">@+android:style/Animation.Translucent</item>
	</style>
</resources>
```
最后一步，把这个styles.xml用在相应的Activity上。即在AndroidManifest.xml中的任意<activity>标签中添加
android:theme = "@style/transparent"
如果想设置所有的activity都使用这个风格，可以把这句标签语句添加在<application>中。
最后运行程序，哈哈，是不是发现整个界面都被蒙上一层半透明了。最后可以把背景色9000换成0000，运行程序后，就全透明了，看得见背景下的所有东西可以却都操作无效。呵呵....
#### 2.Dialog全透明
1.准备保留边框的全透明素材如下图：
2.在values中新建一styles.xml文件，内容如下：
```  
<?xml version="1.0" encoding="UTF-8"?>
<resources>
	<style name="TANCStyle" parent="@android:style/Theme.Dialog">
		<!-- 更换背景图片实现全透明 -->
		<item name="android:windowBackground">@drawable/panel_background_sodino1</item>
		<!-- 屏幕背景不变暗 -->
		<item name="android:backgroundDimEnabled">false</item><!-- 更改对话框标题栏 -->
		<item name="android:windowTitleStyle">@style/TitleStyle</item>
		</style>
		<style name="TitleStyle" parent="@android:style/DialogWindowTitle">
		<item name="android:textAppearance">@style/TitleText</item>
		</style>
		<style name="TitleText" parent="@android:style/TextAppearance.DialogWindowTitle">
		<!-- 设置Dialog标题栏文字颜色。 -->
		<item name="android:textColor">000</item>
	</style>
</resources>
```
3.在layout文件夹下新建一文件句为main_dialog.xml，内容如下：
```  
<?xml version="1.0" encoding="UTF-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="0000" >
    <ScrollView
        android:id="@+id/ScrollView01"
        android:layout_width="wrap_content"
        android:layout_height="200px"
        android:layout_below="@+id/ImageView01"
        android:background="0000" >
        <TextView
            android:id="@+id/TextView01"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:background="0000"
            android:text="SodinoText"
            android:textColor="f000" >
        </TextView>
    </ScrollView>
    <Button
        android:id="@+id/btnCancel"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/ScrollView01"
        android:layout_centerHorizontal="true"
        android:text="Cancel" >
    </Button>
</RelativeLayout>
<?xml version="1.0" encoding="UTF-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="0000" >
    <ScrollView
        android:id="@+id/ScrollView01"
        android:layout_width="wrap_content"
        android:layout_height="200px"
        android:layout_below="@+id/ImageView01"
        android:background="0000" >
        <TextView
            android:id="@+id/TextView01"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:background="0000"
            android:text="SodinoText"
            android:textColor="f000" >
        </TextView>
    </ScrollView>
    <Button
        android:id="@+id/btnCancel"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/ScrollView01"
        android:layout_centerHorizontal="true"
        android:text="Cancel" >
    </Button>
</RelativeLayout>
```
4.Activity代码如下：
```  
import android.app.Activity;
import android.app.Dialog;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
public class TANCAct extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Button btnShow = (Button) findViewById(R.id.btnShow);
		btnShow.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View view) {
				showTANC(
						"This is my custom dialog box",
						"TextContent When a dialog is requested for the first time, Android calls onCreateDialog(int) from your Activity, which is where you should instantiate the Dialog. This callback method is passed the same ID that you passed to showDialog(int). After you create the Dialog, return the object at the end of the method.",
						"http://blog.csdn.net/sodino");
			}
		});
	}
	private void showTANC(String header, String content, String url) {
		final Dialog dialog = new Dialog(this, R.style.TANCStyle);
		dialog.setContentView(R.layout.main_dialog);
		dialog.setTitle(header);
		dialog.setCancelable(true);
		TextView textView01 = (TextView) dialog.findViewById(R.id.TextView01);
		textView01.setText(content + content + content);
		Button btnCancel = (Button) dialog.findViewById(R.id.btnCancel);
		btnCancel.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View view) {
				dialog.cancel();
			}
		});
		dialog.show();
	}
}
```