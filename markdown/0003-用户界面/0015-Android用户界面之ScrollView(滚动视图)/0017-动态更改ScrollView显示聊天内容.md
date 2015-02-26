直接上代码,以下例子可用于如题所示的功能: 
```  
import java.util.Calendar;
import android.app.Activity;
import android.content.Context;
import android.graphics.Color;
import android.os.Bundle;
import android.os.Handler;
import android.text.format.DateFormat;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.LinearLayout;
import android.widget.ProgressBar;
import android.widget.ScrollView;
import android.widget.TextView;
import android.view.View.OnClickListener;
public class fetion2009 extends Activity implements OnClickListener {
	// 进度条控件，但拿出来是为了可控，动态改变其进度
	ProgressBar pb;
	// 聊天对话的底色是间隔的
	private static final int[] bg = { Color.WHITE, Color.GRAY };
	// 聊天对话的底色 当前色应该是bg中的索引值
	private static int bgIndex = 0;
	LinearLayout layout;
	ScrollView scroll;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 聊天对白窗口需要滚动
		scroll = (ScrollView) findViewById(R.id.scrollview);
		Button inputbutton = (Button) findViewById(R.id.inputbutton);
		inputbutton.setOnClickListener(this);
		// 线性布局方式
		layout = new LinearLayout(this);
		// 控件对其方式为垂直排列
		layout.setOrientation(LinearLayout.VERTICAL);
		// 设置布局板的一个特殊颜色，这可以检验我们会话时候是否有地方颜色不正确！
		layout.setBackgroundColor(0xff00ffff);
		// 丰富聊天页面，也顺带测试页面滚动效果，增加了10个重复的对话内容
		for (int i = 0; i < 15; i++) {
			setMsg(layout, this, getCurrColor(), i + "聊天内容在这里....");
		}
		scroll.addView(layout);
	}
	 /**
	  * <a href="\"http://www.eoeandroid.com/home.php?mod=space&uid=7300\""
	  * target="\"_blank\"">@return</a> 当前聊天对白的底色值
	  */
	private int getCurrColor() {
		return bg[(++bgIndex) % bg.length];
	}
	 /**
	  * 动态增加一个聊天内容 这里为了简化编程把 某人说 和 内容放到一个TextView中，
	  * 可以根据设计文档拆成2个TextView分别显示，设置字体等
	  * @param layout
	  *            TextView控件欲添加到的目标layout
	  * @param context
	  *            构建View控件的必须参数 既View控件的环境
	  * @param bgColur
	  *            TextView控件的背景色
	  * @param MSG
	  *            TextView控件要现实的文本内容
	  */
	private void setMsg(LinearLayout layout, Context context, int bgColur,
			String MSG) {
		TextView tv = new TextView(context);
		// 获取一个全局的日历实例，用于获取当前系统时间并格式化成小时：分钟形式，
		// 仅用于测试，这里的时间应该是由其他程序提供
		tv.setText("某人 说: [
				+ DateFormat.format("kk:mm", Calendar.getInstance()) + "]\n
				+ MSG);
		tv.setBackgroundColor(bgColur);
		layout.addView(tv);
	}
	private final Handler mHandler = new Handler();
	@Override
	public void onClick(View v) {
		setMsg(layout, this, getCurrColor(), "聊天内容在这里。。");
		// 投递一个消息进行滚动
		mHandler.post(mScrollToBottom);
	}
	private Runnable mScrollToBottom = new Runnable() {
		@Override
		public void run() {
			Log.d("", "ScrollY: " + scroll.getScrollY());
			int off = layout.getMeasuredHeight() - scroll.getHeight();
			if (off > 0) {
				scroll.scrollTo(0, off);
			}
		}
	};
}
```
2.main.xml文件布局 
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="c8d8f4"
    android:orientation="vertical" >
    <ScrollView
        android:id="@+id/scrollview"
        android:layout_width="fill_parent"
        android:layout_height="380dp"
        android:background="c8d8f4"
        android:text="@string/hello" >
    </ScrollView>
    <EditText
        android:id="@+id/inputText"
        android:layout_width="240dp"
        android:layout_height="wrap_content"
        android:layout_below="@+id/scrollview" />
    <Button
        android:id="@+id/inputbutton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@+id/scrollview"
        android:layout_marginLeft="260dp"
        android:text="发送" />
</RelativeLayout>
```