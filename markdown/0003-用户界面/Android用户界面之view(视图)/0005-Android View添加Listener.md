在开发中为控件添加Listener是非常常见的工作，最简单的添加Listener方式可以这样：
```  
findViewById(R.id.myButton).setOnClickListener(
	new View.OnClickListener() {
		public void onClick(View v) {
			// Do stuff
		}
	});
```
采用上述方法添加Listener有个缺点就是如果控件太多的话，Listener数量也会增多，因此，可以采用如下的小窍门减少Listener的数量：
```  
View.OnClickListener handler = View.OnClickListener() {
	public void onClick(View v) {
		switch (v.getId()) {
			case R.id.Button01:
				// doStuff
				break;
			case R.id.Button02: 
				// doStuff
				break;
		}
	}
}					 
findViewById(R.id.myButton).setOnClickListener(handler);
findViewById(R.id.myOtherButton).setOnClickListener(handler);
```
在Android1.6里面，添加Listener的工作变得相当的简单，具体步骤如下：
1.首先在layout里面定义Button并指定响应的Listener
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello" />
    <Button
        android:id="@+id/Button01"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:onClick="myClickHandler01"
        android:text="Button01" />
    <Button
        android:id="@+id/Button02"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:onClick="myClickHandler02"
        android:text="Button02" />
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello" />
</LinearLayout>
```
其中以下这两行就是新增的特性：
```  
android:onClick="myClickHandler01"
android:onClick="myClickHandler02"
```
2.在活动里面定义public的方法myClickHandler01、和myClickHandler02（注意这两个方法必须有一个View的形参）。
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
public class TestOnClickListener extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
	}
	public void myClickHandler01(View target) {
		setTitle("myClickHandler01");
	}
	public void myClickHandler02(View target) {
		setTitle("myClickHandler02");
	}
}
```
当然，你也可以采用这种写法：
将两个按钮设置到同一个Listener
```  
android:onClick="myClickHandler"
android:onClick="myClickHandler"
```
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
public class TestOnClickListener extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
	}
	public void myClickHandler(View target) {
		switch (target.getId()) {
		case R.id.Button01:
			setTitle("myClickHandler01");
			break;
		case R.id.Button02:
			setTitle("myClickHandler02");
			break;
		}
	}
}
```