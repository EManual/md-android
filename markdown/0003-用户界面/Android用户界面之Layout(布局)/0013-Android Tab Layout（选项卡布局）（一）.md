为了创建一个选项卡的UI，你需要使用一个TabHost和一个TabWidget，TabHost必须是布局文件的根节点，它包含了为了显示选项卡的TabWidget和一个用于显示选项内容的FrameLayout。
你可以用一或两种方法实现你的选项卡内容：在用一个Activity中用选项卡来在视图之间切换，或者用用选项卡来改变所有的分离的Activity。你根据你的需求来使用你想在程序中的方法，但是如果每个选项卡提供一个独特的用户Activity，那么为每个选项卡实现独立的Activity是有意义的，所有你最好在你的离散群里管理应用程序，要好过使用大量的应用程序和布局文件。
在这个例子中，你可以创建一个为每个单独的Activity创建选项卡来创建一个选项卡UI。
1、开始一个新的工程，叫做HelloTabWidget。
2、第一，创建三个独立的Activity程序在你的工程中：ArtistsActivity,AlbumsActivity,和SongsActivity，他们每个代表一个单独的选项卡，现在用TextView来没每个程序显示一个简单的信息，比如：
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.TextView;
public class AlbumsActivity extends Activity {
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		TextView textview = new TextView(this);
		textview.setText("This is the Albums tab");
		setContentView(textview);
	}
}
public class SongsActivity extends Activity {
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		TextView textview = new TextView(this);
		textview.setText("This is the Songs tab");
		setContentView(textview);
	}
}
public class ArtistsActivity extends Activity {
	[Tags]/** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		TextView textview = new TextView(this);
		textview.setText("This is the Artists tab");
		setContentView(textview);
	}
}
```
注意这个例子中不需要布局文件，只需要创建一个TextView，并且为文本赋值即可。重复创建三个类似的Activity，并且要在AndroidManifest.xml文件中注册，否则报错