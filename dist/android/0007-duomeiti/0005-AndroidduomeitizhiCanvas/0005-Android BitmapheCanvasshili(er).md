代码如下：
```  
public void setValues(double[] values, double maxValue) { // 设置显示范围，下文提到
	mValues = values.clone();
	for (int i = 0; i < values.length; i++) {
		mValues = maxValue;
	}
}
@Override
public void onDraw(Canvas canvas) { // 重写onDraw直接绘制
	Log.i(TAG, "onDraw: w = " + getWidth() + ", h = " + getHeight());
	int xmin = getPaddingLeft();
	int xmax = getWidth() - getPaddingRight();
	int ymin = getPaddingTop();
	int ymax = getHeight() - getPaddingBottom();
	int startx = xmin;
	for (int i = 0; i < mValues.length; i++) {
		int endx = xmin + (int) (mValues * (xmax - xmin));
		canvas.drawRect(startx, ymin, endx, ymax, sPaint); // 通过canvas绘制范围
		// 该方法原型 drawRect(float left, float top, float right, float bottom,
		// Paint paint)
		startx = endx;
	}
	super.onDraw(canvas);
}
```
调用方法很简单，和普通的Button没有什么区别，这里我们仅仅多定义了setValues方法，eoeAndroid提醒网哟注意布局文件xml中如何定义，在最下文
```  
private GraphableButton mButtons;
mButtons = (GraphableButton) findViewById(R.id.button0);
mButtons.setOnClickListener(this); //设置一个按下事件监听
mButtons.setVisibility(View.INVISIBLE); //设置当前按钮不可见
mButtons.setText("android123.com欢迎您");
mButtons.setValues(0,100);
mButtons.setVisibility(View.VISIBLE); //设置按钮可见
```
下面在layout.xml中如何写呢，这里要写上自己程序完整的package name才能正确被adt识别，相关的具体定义如下:
```  
android:id="@+id/button7"
android:layout_width="fill_parent"
android:layout_height="0dp"
android:layout_marginLeft="4dp"
android:layout_marginRight="4dp"
android:layout_marginBottom="4dp"
android:layout_weight="1"
```