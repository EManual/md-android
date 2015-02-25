其实单线程模型就是默认情况下android把所有操作都放在主线程也就是UI Thread线程中来执行，如果你想O上边那段不是说它会阻塞用户界面嘛。
那我可以另起一个线程来执行一些操作 没错你的想法非常good。那么接下来，你就会尝试另起一个线程来执行一些操作。
OK 结果就有两种可能 
一：你在另外开启的那个线程中执行了一些后台的操作 比如开启一个服务啊。那么恭喜你 你成功了。 
二：第二种可能结果就是你会收到一个华丽的异常。
下面我们就通过一个小例子来说明这个华丽的异常时怎么回事？ 
因为本篇文章的例子比较多所以我为了大家好找 给例子起了名称 这个例子的名称是：异常测试 
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/textview01"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello" />
    <Button
        android:id="@+id/myButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/textview01"
        android:text="异常测试" />
    <TextView
        android:id="@+id/myTextView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignTop="@id/myButton"
        android:layout_toRightOf="@id/myButton"
        android:textColor="FF0000"
        android:textSize="15pt" />
</RelativeLayout>
```
java代码：
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.TextView;
public class Activity01 extends Activity {
	private Button myButton;
	private TextView myTextView;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		myButton = (Button) findViewById(R.id.myButton);
		myTextView = (TextView) findViewById(R.id.myTextView);
		myButton.setOnClickListener(new MyButtonListener());
	}
	class MyButtonListener implements OnClickListener {
		@Override
		public void onClick(View v) {
			new Thread() {

				@Override
				public void run() {
					// 我们在这里更新了UI 设置了TextView的值
					myTextView.setText("张三");
				}
			}.start();
		}
	}
}
```