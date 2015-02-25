我们大家应该知道的是，如果把滚动条控件加载到我们的读书软件里，就会呈现出非常好的效果。在用户使用时就不必为手动翻页而烦恼了，这个也是android种非常基础的一个控件，我们要掌握好这个空间的用法，这样才能把android中每个控件都加在android软件里，这样才能让我们的软件更加完美，下面的代码非常的简单。我们来看看代码：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <!--
		ScrollView - 滚动条控件
		scrollbarStyle - 滚动条的样式
    -->
    <ScrollView
        android:id="@+id/scrollView"
        android:layout_width="fill_parent"
        android:layout_height="200px"
        android:background="@android:drawable/edit_text"
        android:scrollbarStyle="outsideOverlay" >
        <TextView
            android:id="@+id/textView"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content" />
    </ScrollView>
</LinearLayout>
```
代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
import android.widget.TextView;
public class _ScrollView extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		this.setContentView(R.layout.scrollview);
		setTitle("ScrollView");
		TextView textView = (TextView) this.findViewById(R.id.textView);
		textView.setText("a\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na\na");
	}
}
```