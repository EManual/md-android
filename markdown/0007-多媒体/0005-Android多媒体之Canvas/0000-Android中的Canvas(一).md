大家都知道在我们要显示一个自己定义的View有2中方法：
第一种：是直接new一个我们的View对象并且setContentView(myView); 假如我们自己定义的View对象叫myView。
其实我们在Activity里边就2行代码就搞定了。
```  
MyView myView = new MyView(this); 
setContentView(myView); 
```
第二种方式就是 把它放到我们的布局文件中，例如这样
```  
<android.demo.MyView 
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	/>
```
其中eoe.demo是我们的包名。 用这种方式 必须在我们自定义的View类也就是MyView里边 加上这样个构造方法
```  
public MyView(Context context, AttributeSet attributeSet){ 
	super(context, attributeSet); 
} 
```
我们现在就来看看下面的一个实例，我们先来看看效果图：
![img](P)  
![img](P)  
就是 一个图像旋转的例子，我们来看看代码吧：
```  
import android.app.Activity;
import android.os.Bundle;
public class testActivity extends Activity {
	private testView mTestview;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		mTestview = (testView) findViewById(R.id.testView);
		mTestview.initBitmap(320, 240, 0xcccccc);
	}
}
```
布局文件 
```  
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <testView
        android:id="@+id/testView"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        tileSize="12" />
</FrameLayout>
```
testView类 这个类就是我们自己定义的View类了 这里我们把它放在布局文件中加载进来 
```  
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Matrix;
import android.graphics.Paint;
import android.graphics.Typeface;
import android.graphics.Bitmap.Config;
import android.util.AttributeSet;
import android.view.View;
public class testView extends View {
	private Bitmap mbmpTest = null;
	private final Paint mPaint = new Paint();
	private final String mstrTitle = "感受Android带给我们的新体验";
	public testView(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
		mPaint.setColor(Color.GREEN);
	}
	public testView(Context context, AttributeSet attrs) {
		super(context, attrs);
		mPaint.setColor(Color.GREEN);
	}
	public boolean initBitmap(int w, int h, int c) {
		// 返回具有指定宽度和高度可变的位图,它的初始密度可以调用getDensity()
		mbmpTest = Bitmap.createBitmap(w, h, Config.ARGB_8888);
		// 把一个具有指定的位图绘制到画布上。位图必须是可变的。
		// 在画布最初的目标密度是与给定的位图的密度相同,返回一个具有指定位图的画布
		Canvas canvas = new Canvas(mbmpTest);
		// 设置画布的颜色
		canvas.drawColor(Color.WHITE);
		Paint p = new Paint();
		String familyName = "宋体";
		Typeface font = Typeface.create(familyName, Typeface.BOLD);
		p.setColor(Color.RED);
		p.setTypeface(font);
		p.setTextSize(22);
		// 0,100指定文字的起始位置
		canvas.drawText(mstrTitle, 0, 100, p);
		return true;
	}
	@Override
	public void onDraw(Canvas canvas) {
		super.onDraw(canvas);
		if (mbmpTest != null) {
			Matrix matrix = new Matrix();
			// matrix.postScale(0.5f, 0.5f);
			// 以 120，120这个点位圆心 旋转90度
			// matrix.setRotate(90,120,120);
			// 使用指定的矩阵绘制位图
			canvas.drawBitmap(mbmpTest, matrix, mPaint);
		}
	}
}
```