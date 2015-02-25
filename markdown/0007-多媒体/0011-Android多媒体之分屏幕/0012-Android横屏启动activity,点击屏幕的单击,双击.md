横屏启动activity
方法1：在androidmanyfest.xml的activity中加入属性 android:screenOrientation="landscape"
方法2：在oncreate中加入如下代码 if(getRequestedOrientation()!=ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE){ setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);}
屏幕的单击和双击事件
```  
mGestureDetector = new GestureDetector(new SimpleOnGestureListener(){
	@Override
	public boolean onDoubleTap(MotionEvent e) {
		return true;
	}
	@Override
	public boolean onSingleTapConfirmed(MotionEvent e) {
		return true;
	}
	@Override
	public void onLongPress(MotionEvent e) {
	});
}
```
大家可不要小看了这么个例子哦，这个例子可是很有用的，点击事件是我们开发中必不可少的一个监听，大家可要用好它哦，新手们也得好好看哦，也不能骄傲，太骄傲了到最后可要给自己填很多麻烦的哦。