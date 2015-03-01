在做android应用的开发的时候，横向滚动或者要做出跑马灯的效果很简单，textview本身的属性就支持，只要设置准确就会滚动，开发起来比较简单，但是textview不支持垂直滚动，那么垂直滚动就需要自己来实现了，很多网友提供的垂直滚　动方案都是千篇一律，使用ScrollView来进行滚动，但是都不完美，做起来有些别扭。有一位网友给出的歌词的滚动思路明确，能从根本上解决问题，因此我实现的这个滚动是在这位网友的基础上实现，封装了一个View，view继承自TextView。
实现图中效果的关键点是：
１、重写onDrow方法，计算每次的滚动的距离。
２、计算view的Y轴的重点，让当前显示的处于高亮显示状态。
３、定时的刷新View使其界面不断的刷先，出现滚动的效果。
４、实现数据结构，将数据传给view。
下面看看主要代码：
#### １、创建一个类继承TextView
```  
 /**
  * 垂直滚动的TextView Widget
  */
public class VerticalScrollTextView extends TextView {
```
#### 2、实现构造函数：
```  
public VerticalScrollTextView(Context context) {
	super(context);
	init();
}
public VerticalScrollTextView(Context context, AttributeSet attr) {
	super(context, attr);
	init();
}
public VerticalScrollTextView(Context context, AttributeSet attr, int i) {
	super(context, attr, i);
	init();
}
private void init() {
	setFocusable(true);
	// 这里主要处理如果没有传入内容显示的默认值
	if (list == null) {
		list = new ArrayList<Notice>();
		Notice sen = new Notice(0, "暂时没有通知公告");
		list.add(0, sen);
	}
	// 普通文字的字号，以及画笔颜色的设置
	mPaint = new Paint();
	mPaint.setAntiAlias(true);
	mPaint.setTextSize(16);
	mPaint.setColor(Color.BLACK);
	mPaint.setTypeface(Typeface.SERIF);
	// 高亮文字的字号，以及画笔颜色的设置
	mPathPaint = new Paint();
	mPathPaint.setAntiAlias(true);
	mPathPaint.setColor(Color.RED);
	mPathPaint.setTextSize(16);
	mPathPaint.setTypeface(Typeface.SANS_SERIF);
}
```
#### ３、从写onDraw方法，并计算文字的行距，并且将将普通文字和高亮文字，在这个方法中绘制出来
```  
protected void onDraw(Canvas canvas) {
	super.onDraw(canvas);
	canvas.drawColor(0xEFeffff);
	Paint p = mPaint;
	Paint p2 = mPathPaint;
	p.setTextAlign(Paint.Align.CENTER);
	if (index == -1)
		return;
	p2.setTextAlign(Paint.Align.CENTER);
	canvas.drawText(list.get(index).getName(), mX, middleY, p2);
	float tempY = middleY;
	for (int i = index - 1; i >= 0; i--) {
		tempY = tempY - DY;
		if (tempY < 0) {
			break;
		}
		canvas.drawText(list.get(i).getName(), mX, tempY, p);
	}
	tempY = middleY;
	for (int i = index + 1; i < list.size(); i++) {
		tempY = tempY + DY;
		if (tempY > mY) {
			break;
		}
		canvas.drawText(list.get(i).getName(), mX, tempY, p);
	}
}
```
#### ４、计算Y轴中值以及更新索引
```  
protected void onSizeChanged(int w, int h, int ow, int oh) {
	super.onSizeChanged(w, h, ow, oh);
	mX = w * 0.5f;
	mY = h;
	middleY = h * 0.5f;
}
private long updateIndex(int index) {
	if (index == -1)
		return -1;
	this.index = index;
	return index;
}
```
#### ５、定时更新view，并将接口暴露给客户程序调用。
```  
public void updateUI() {
	new Thread(new updateThread()).start();
}
class updateThread implements Runnable {
	long time = 1000;
	int i = 0;
	public void run() {
		while (true) {
			long sleeptime = updateIndex(i);
			time += sleeptime;
			mHandler.post(mUpdateResults);
			if (sleeptime == -1)
				return;
			try {
				Thread.sleep(time);
				i++;
				if (i == getList().size())
					i = 0;
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
}
Handler mHandler = new Handler();
Runnable mUpdateResults = new Runnable() {
	public void run() {
		invalidate();
	}
};
```
#### ６、xml布局文件中调用：
```  
<?xml version="1.0" encoding="utf-8"?>
<!-- Demonstrates scrolling with a ScrollView. -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <com.demo.xsl.text.SampleView
        android:id="@+id/sampleView1"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:background="@drawable/selector" />
</LinearLayout>
```
#### ７、java代码中调用，传递数据：
```  
import java.util.ArrayList;
import java.util.List;
import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
public class VerticalScrollTextActivity extends Activity {
	SampleView mSampleView;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		mSampleView = (SampleView) findViewById(R.id.sampleView1);
		List lst = new ArrayList<Sentence>();
		for (int i = 0; i < 30; i++) {
			if (i % 2 == 0) {
				Sentence sen = new Sentence(i, i + "、金球奖三甲揭晓 C罗梅西哈维入围 ");
				lst.add(i, sen);
			} else {
				Sentence sen = new Sentence(i, i + "、公牛欲用三大主力换魔兽？？？？");
				lst.add(i, sen);
			}
		}
		// 给View传递数据
		mSampleView.setList(lst);
		// 更新View
		mSampleView.updateUI();
	}
}
```