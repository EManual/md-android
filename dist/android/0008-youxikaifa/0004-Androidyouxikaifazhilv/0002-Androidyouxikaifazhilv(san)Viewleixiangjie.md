在android游戏开发之旅二中我们讲到了View和SurfaceView的区别，今天Android123从View类开始着重的介绍Android图形显示基类的相关方法和注意点。
自定义View的常用方法:
```  
onFinishInflate() 当View中所有的子控件均被映射成xml后触发
onMeasure(int, int) 确定所有子元素的大小
onLayout(boolean, int, int, int, int) 当View分配所有的子元素的大小和位置时触发
onSizeChanged(int, int, int, int) 当view的大小发生变化时触发
onDraw(Canvas) view渲染内容的细节
onKeyDown(int, KeyEvent) 有按键按下后触发
onKeyUp(int, KeyEvent) 有按键按下后弹起时触发
onTrackballEvent(MotionEvent) 轨迹球事件
onTouchEvent(MotionEvent) 触屏事件
onFocusChanged(boolean, int, Rect) 当View获取或失去焦点时触发 
onWindowFocusChanged(boolean) 当窗口包含的view获取或失去焦点时触发
onAttachedToWindow() 当view被附着到一个窗口时触发
onDetachedFromWindow() 当view离开附着的窗口时触发，Android123提示该方法和onAttachedToWindow() 是相反的。
onWindowVisibilityChanged(int) 当窗口中包含的可见的view发生变化时触发
```
以上是View实现的一些基本接口的回调方法，一般我们需要处理画布的显示时，重写onDraw(Canvas)用的的是最多的：
```  
@Override
protected void onDraw(Canvas canvas) {
	// 这里我们直接使用canvas对象处理当前的画布，比如说使用Paint来选择要填充的颜色
	Paint paintBackground = new Paint();
	paintBackground.setColor(getResources().getColor(R.color.xxx));// 从Res中找到名为xxx的color颜色定义
	canvas.drawRect(0, 0, getWidth(), getHeight(), paintBackground); // 设置当前画布的背景颜色为paintBackground中定义的颜色，以0,0作为为起点，以当前画布的宽度和高度为重点即整块画布来填充，具体的请查看Android123未来讲到的Canvas和Paint，在Canvas中我们可以实现画路径，图形，区域，线。而Paint作为绘画方式的对象可以设置颜色，大小，甚至字体的类型等等。
}
```
当然还有就是处理窗口还原状态问题(一般用于横竖屏切换)，除了在Activity中可以调用外，开发游戏时我们尽量在View中使用类似
```  
@Override
protected Parcelable onSaveInstanceState() {
	Parcelable p = super.onSaveInstanceState();
	Bundle bundle = new Bundle();
	bundle.putInt("x", pX);
	bundle.putInt("y", pY);
	bundle.putParcelable("android123_state", p);
	return bundle;
}
@Override
protected void onRestoreInstanceState(Parcelable state) {
	Bundle bundle = (Bundle) state;
	dosomething(bundle.getInt("x"), bundle.getInt("y")); // 获取刚才存储的x和y信息
	super.onRestoreInstanceState(bundle.getParcelable("android123_state"));
	return;
}
```