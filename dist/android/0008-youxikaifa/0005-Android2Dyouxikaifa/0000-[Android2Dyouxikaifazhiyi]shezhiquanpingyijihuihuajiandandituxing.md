直接上代码：
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.Window;
import android.view.WindowManager;
public class MainActivity extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		this.getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
				WindowManager.LayoutParams.FLAG_FULLSCREEN);
		// 隐去电池等图标和一切修饰部分（状态栏部分）
		this.requestWindowFeature(Window.FEATURE_NO_TITLE);
		// 隐去标题栏（程序的名字）
		setContentView(new MyView(this));
	}
}
```
注意： 隐去标题（应用的名字) 此设定必须要写在setContentView之前，否则会有异常!
对于设置全屏，主要就两点:
一点是设置隐去状态栏部分，包括电池等图标，第二点无疑就是把我们应用的名字也隐去不显示，这样一来就全屏了。
```  
import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.graphics.Rect;
import android.graphics.RectF;
import android.view.View;
public class MyView extends View {
	private Paint paint;
	public MyView(Context context) {
		super(context);
		paint = new Paint();
		paint.setAntiAlias(true);// 设置画笔无锯齿(如果不设置可以看到效果很差)
		this.setKeepScreenOn(true);// 设置背景常亮
		paint.setColor(Color.RED);
	}
	@Override
	public void draw(Canvas canvas) {
		super.draw(canvas);
		canvas.drawColor(Color.WHITE);// 设置刷屏颜色
		Rect rect = new Rect(30, 30, 50, 50); // 这里最后两个参数不是宽高、而是矩形右下角的坐标
		canvas.drawRect(rect, paint);
		RectF rectF = new RectF(70f, 30f, 90f, 90f);// RectF 只是矩形 float形式
													// 只是跟Rect精确度不一样
		canvas.drawArc(rectF, 0, 360, true, paint);
		canvas.drawCircle(150, 30, 20, paint);// 这也是画圆 第三个参数为半径
		float[] points = new float[] { 200f, 10f, 200f, 40f, 300f, 30f, 400f,
				70f };
		canvas.drawLines(points, paint);
		// canvas.drawLines(points, 1, 4, paint);//选取特定点数组中两点来画出一条直线
		canvas.drawText("Himi", 230, 30, paint);
	}
}
```
这里几行代码比较简单，这里就不多说了，下一节准备制作一个游戏注册，输入游戏帐号帐号密码的一个界面。