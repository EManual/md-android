刚才上边说到我们有一个首选项视图，就是那个FlightPreferenceActivity，在这个例子中我们是通过一个菜单跳转到我们的首选项视图的。就是我们有一个MainActivity 这个MainActivity有一个菜单叫Settings当我们点击这个菜单的时候就会跳转到我们的首选项视图了，然后我们修改首选项的内容 当我们再一次回到 MainActivity 的时候就会看到我们修改后的 文本和值，我们通过一个TextView来显示我们还是来看一下效果吧。

![img](http://emanual.github.io/md-android/img/data_preference/07_preference.jpg) 

下面这个XML文件就是用来定义我们 这个菜单的，此文件的目录在 /res/menu/mainmenu.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:id="@+id/menu_prefs"
        android:title="@string/menu_prefs_title"/>
    <item
        android:id="@+id/menu_quit"
        android:title="@string/menu_quit_title"/>
</menu>
```
在接下来就是我们的布局文件main.xml了
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/text1"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
</LinearLayout>
```
只有一个TextView 主要用来显示我们修改首选项之后的文本和值。有了main.xml自然少不了MainActivity了，下面使我们的MainActivity类
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.TextView;
public class MainActivity extends Activity {
	private TextView tv = null;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
	}
}
```