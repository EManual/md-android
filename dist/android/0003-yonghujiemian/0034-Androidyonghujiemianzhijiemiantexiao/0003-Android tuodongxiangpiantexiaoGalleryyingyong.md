在android.util.Log常用的方法有以下5个：Log.v()、Log.d()、Log.i()、Log.w()以及Log.e()。根据首字母对应VERBOSE,DEBUG,INFO,WARN,ERROR。
1、Log.v 的调试颜色为黑色的，任何消息都会输出大家是全能看见的，这里的v代表verbose的意思，平时使用就是Log.v("","");
2、Log.d的输出颜色是蓝色的，这个主要就是输出debug调试的意思，但它会输出上层的信息，过滤起来可以通过DDMS的Logcat标签来选择.
3、Log.i的输出为绿色，这个就是提示性的消息information，它是不会输出Log.v和Log.d的信息，但它会显示i、w和e的信息
4、Log.w的意思为橙色，我们主要看作为warning警告的意思，一般需要我们注意优化Android代码，同时选择它后还会帮我们输出Log.e的信息。
5、Log.e为红色，可以想到error错误，这里仅显示红色的错误信息，这些错误就需要我们认真的分析，查看主要的信息了。
准备工作(打开LogCat视窗).
启动Eclipse,在Window->Show View会出来一个对话框，当我们点击Ok按钮时，会在控制台窗口出现LogCat视窗.
新建一个Android工程，命名为LogDemo.
设计UI界面，我们在这里就加了一个Button按钮(点击按钮出现Log日志信息).
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
        android:id="@+id/bt"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Presse Me Look Log" />
</LinearLayout>　
```
设计主类为LogDemo.java,
```  
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
public class LogDemo extends Activity {
	private static final String ACTIVITY_TAG = "LogDemo";
	private Button bt;
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 通过findViewById找到Button资源
		bt = (Button) findViewById(R.id.bt);
		// 增加事件响应
		bt.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				Log.v(LogDemo.ACTIVITY_TAG, "This is Verbose.");
				Log.d(LogDemo.ACTIVITY_TAG, "This is Debug.");
				Log.i(LogDemo.ACTIVITY_TAG, "This is Information");
				Log.w(LogDemo.ACTIVITY_TAG, "This is Warnning.");
				Log.e(LogDemo.ACTIVITY_TAG, "This is Error.");
			}
		});
	}
}
```
效果图：
![img](P)  