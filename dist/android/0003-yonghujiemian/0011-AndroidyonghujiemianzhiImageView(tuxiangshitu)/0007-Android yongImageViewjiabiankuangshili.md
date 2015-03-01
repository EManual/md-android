代码如下：
```  
import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.graphics.Rect;
import android.util.AttributeSet;
import android.widget.ImageView;
public class myImageView extends ImageView {
	private String namespace = "http://shadow.com";
	private int color;
	public myImageView(Context context, AttributeSet attrs) {
		super(context, attrs);
		color = Color.parseColor(attrs.getAttributeValue(namespace,
				"BorderColor"));
	}
	@Override
	protected void onDraw(Canvas canvas) {
		super.onDraw(canvas);
		// 画边框
		Rect rec = canvas.getClipBounds();
		rec.bottom--;
		rec.right--;
		Paint paint = new Paint();
		paint.setColor(color);
		paint.setStyle(Paint.Style.STROKE);
		canvas.drawRect(rec, paint);
	}
}
```
这里要注意的是super.onDraw(canvas);在前，否则边框可能会被图片所覆盖。
```  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:shadow="http://shadow.com"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="@drawable/bg_newslist" >
    <RelativeLayout
        android:id="@id/LinerLayout01"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" >
        <shadow.widget.myImageView
            android:id="@id/newsitemPicWithBorder"
            android:layout_width="80px"
            android:layout_height="60px"
            android:layout_alignParentRight="true"
            android:layout_centerInParent="true"
            android:layout_marginRight="3px"
            android:src="@drawable/image_loading"
            shadow:BorderColor="GRAY" >
        </shadow.widget.myImageView>
    </RelativeLayout>
</LinearLayout>
```
设置边框颜色 shadow:BorderColor="GRAY" 