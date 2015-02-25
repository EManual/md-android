代码如下：
```  
import Android.content.Context;
import Android.graphics.Bitmap;
import Android.graphics.BitmapFactory;
import Android.graphics.Canvas;
import Android.graphics.Color;
import Android.graphics.Matrix;
import Android.graphics.Paint;
import Android.graphics.drawable.BitmapDrawable;
import Android.view.MotionEvent;
import Android.widget.ImageView;
[Tags]/**
 [Tags]* ImageAdapter中ImageView的实现类
 [Tags]*/
public class ImageViewImp extends ImageView {
	private int alpha = 250;
	private boolean pressed = false;
	public ImageViewImp(Context context) {
		super(context);
	}
	Matrix m;
	public void show() {
		new Thread() {
			public void run() {
				int time = 2000;
				try {
					pressed = true;
					while (time > 0) {
						Thread.sleep(200);
						time -= 200;
						alpha -= 25;
						postInvalidate();
					}
					pressed = false;
				} catch (Exception e) {
					e.printStackTrace();
				}
			};
		}.start();
	}
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		if (event.getAction() == MotionEvent.ACTION_DOWN)
			show();
		return false;
	}
	@Override
	protected void onDraw(Canvas canvas) {
		Paint p = new Paint();
		p.setColor(Color.WHITE);
		p.setStyle(Paint.Style.STROKE);
		p.setStrokeWidth(10);
		BitmapDrawable bd = (BitmapDrawable) getDrawable();
		if (bd != null) {
			canvas.drawBitmap(imageScale(bd.getBitmap(), 107, 113), 21, 18, p);
		}
		canvas.drawBitmap(BitmapFactory.decodeResource(getContext()
				.getResources(), R.drawable.kua), 0, 0, p);
		if (isPressed()) {
			canvas.drawRect(5, 5, 140, 140, p);
		}
		if (pressed) {
			p.setAlpha(alpha);
			canvas.drawRect(5, 5, 140, 140, p);
		}
	}
	public static Bitmap imageScale(Bitmap bitmap, int dst_w, int dst_h) {
		int src_w = bitmap.getWidth();
		int src_h = bitmap.getHeight();
		float scale_w = ((float) dst_w) / src_w;
		float scale_h = ((float) dst_h) / src_h;
		Matrix matrix = new Matrix();
		matrix.postScale(scale_w, scale_h);
		Bitmap dstbmp = Bitmap.createBitmap(bitmap, 0, 0, src_w, src_h, matrix,
				true);
		return dstbmp;
	}
}
```
各种xml ->布局-> style -> color
```  
<?xml version="1.0" encoding="utf-8"?>
<!-- main.xml 在layout下 -->
<LinearLayout xmlns:Android="http://schemas.android.com/apk/res/android"
    android:id="@+id/twill"
    Android:layout_width="fill_parent"
    Android:layout_height="fill_parent"
    Android:background="@android:color/transparent"
    Android:orientation="vertical"
    android:gravity="top" >
    <Gallery
        Android:id="@+id/myggg"
        Android:layout_width="fill_parent"
        Android:layout_height="wrap_content" />
</LinearLayout>
<?xml version="1.0" encoding="utf-8"?> 
<!--style.xml 在values下--> 
<resources > 
<style name="testStyle"> 
	<item name="Android:textColor" >EC9237</item>
</style> 
<style name="transparent"> 
	<item name="Android:windowBackground">@drawable/translucent_background</item>
	<item name="Android:windowIsTranslucent">true</item>
</style> 
</resources> 
<?xml version="1.0" encoding="utf-8"?> 
<!--color.xml 在values下--> 
<resources>
	<drawable name="c1">FFFFFFFF</drawable>
	<drawable name="c2">00FFFF</drawable>
	<drawable name="translucent_background">7F000000</drawable>
</resources> 
```