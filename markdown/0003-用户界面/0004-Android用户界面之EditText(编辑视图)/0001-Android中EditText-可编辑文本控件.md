我们这片文章主要讲的就是edittext这个控件，edittext这个控件是可编辑文本控件，就是说我们可以在这个控件里编辑自己想要的东西，这个控件主要用在了短信方面(这个是我个人的观点)。在发短信的时候，你就能用到这个控件了，在你的XML里编辑它，你就可以想得到你想的一个带edittext布局的界面了，不多说了，我们来看看代码就知道了。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
	<!-- EditText - 可编辑文本控件 -->
    <EditText
        android:id="@+id/editText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" >
    </EditText>
</LinearLayout>
```
代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.EditText;
public class _EditText extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		this.setContentView(R.layout.edittext);
		setTitle("EditText");
		EditText txt = (EditText) this.findViewById(R.id.editText);
		txt.setText("我可编辑");// 这里就是你想编辑的地方
	}
}
```