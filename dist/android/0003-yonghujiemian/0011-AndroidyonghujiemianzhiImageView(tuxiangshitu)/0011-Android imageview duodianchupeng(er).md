代码如下：
```  
if (disX != 0 || disY != 0) {
	trans = new TranslateAnimation(disX, 0, disY, 0);
	trans.setDuration(500);
	this.startAnimation(trans);
}
case MotionEvent.ACTION_POINTER_UP: 
	mode = NONE;
	break; 
case MotionEvent.ACTION_POINTER_UP: 
	mode = NONE;
	break; 
case MotionEvent.ACTION_MOVE: 
	/*处理拖动*/
	if (mode == DRAG) {
		if(Math.abs(stop_x-start_x-getLeft())<88 && Math.abs(stop_y - start_y-getTop())<85)
		{
			this.setPosition(stop_x - start_x, stop_y - start_y, stop_x + this.getWidth() - start_x, stop_y - start_y + this.getHeight()); 
			stop_x = (int) event.getRawX();
			stop_y = (int) event.getRawY();
		} 
	} 
	/*处理缩放*/
	else if (mode == ZOOM) { 
		if(spacing(event)>10f)
		{
			afterLenght = spacing(event);
			float gapLenght = afterLenght - beforeLenght; 
			if(gapLenght == 0) {
				break; 
			} 
			else if(Math.abs(gapLenght)>5f) 
			{
				if(gapLenght>0) {
					this.setScale(scale,BIGGER); 
				}else { 
					this.setScale(scale,SMALLER); 
				} 
				beforeLenght = afterLenght;
			} 
		} 
	} 
break; } 
return true; 
 /**
  * 实现处理缩放
  */
private void setScale(float temp, int flag) {
	if (flag == BIGGER) {
		this.setFrame(this.getLeft() - (int) (temp * this.getWidth()),
				this.getTop() - (int) (temp * this.getHeight()),
				this.getRight() + (int) (temp * this.getWidth()),
				this.getBottom() + (int) (temp * this.getHeight()));
	} else if (flag == SMALLER) {
		this.setFrame(this.getLeft() + (int) (temp * this.getWidth()),
				this.getTop() + (int) (temp * this.getHeight()),
				this.getRight() - (int) (temp * this.getWidth()),
				this.getBottom() - (int) (temp * this.getHeight()));
	}
}
 /**
  * 实现处理拖动
  */
private void setPosition(int left, int top, int right, int bottom) {
	this.layout(left, top, right, bottom);
}
```
一个layout
```  
import Android.app.Activity;
import Android.content.Context;
import Android.graphics.Bitmap;
import Android.graphics.BitmapFactory;
import Android.view.View;
import Android.widget.AbsoluteLayout;
import Android.widget.ImageView.ScaleType;
 /**
  * 一个绝对布局
  */
@SuppressWarnings("deprecation")
public class ViewScroll extends AbsoluteLayout {
	private int screenW;
	// 可用的屏幕宽
	private int screenH;
	// 可用的屏幕高 总高度-上面组件的总高度
	private int imgW;
	// 图片原始宽
	private int imgH;
	// 图片原始高
	private TouchView tv;
	public ViewScroll(Context context, int resId, View topView) {
		super(context);
		screenW = ((Activity) context).getWindowManager().getDefaultDisplay().getWidth();
		screenH = ((Activity) context).getWindowManager().getDefaultDisplay().getHeight()
				- (topView == null ? 190 : topView.getBottom() + 50);
		tv = new TouchView(context, screenW, screenH);
		tv.setImageResource(resId);
		Bitmap img = BitmapFactory.decodeResource(context.getResources(), resId);
		imgW = img.getWidth();
		imgH = img.getHeight();
		int layout_w = imgW > screenW ? screenW : imgW;
		// 实际显示的宽
		int layout_h = imgH > screenH ? screenH : imgH;
		// 实际显示的高
		if (layout_w == screenW || layout_h == screenH)
			tv.setScaleType(ScaleType.FIT_XY);
		tv.setLayoutParams(new AbsoluteLayout.LayoutParams(layout_w, layout_h,
				layout_w == screenW ? 0 : (screenW - layout_w) / 2,
				layout_h == screenH ? 0 : (screenH - layout_h) / 2));
		this.addView(tv);
	}
}
```