让Button 有按下效果 更有视觉效果
根据各种状态 定制化所显示的*.png命名为：myselection.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
	<item
		android:state_pressed="false"
		android:drawable="@drawable/play" />
	<item
		android:state_pressed="true"
		android:drawable="@drawable/play_down" />
	<item
		android:drawable="@drawable/play" />
</selector> 
```
在main.xml布局中添加Button元件并设置使用myselection.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Button Style!" />
    <ImageButton
        android:id="@+id/playorpause"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="00000000"
        android:src="@xml/myselection" />
</LinearLayout>
```
其实除了上面的方还有一个方法为：
1. 在maun.xml 中添加ImageButton且不设置使用的*.png
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <ImageButton
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
</LinearLayout>
```
2. 在该ImageButton上设置监听器 并根据其状态使用对应的资源 但是必须要设置默认资源
```  
ImageButton btn = (ImageButton) findViewById(R.id.button);
// to set its default *.png
btn.setBackgroundResource(R.drawable.play);
btn.setOnTouchListener(new ImageButton.OnTouchListener() {
	@Override
	public boolean onTouch(View arg0, MotionEvent arg1) {
		if (arg1.getAction() == MotionEvent.ACTION_DOWN) {
			arg0.setBackgroundResource(R.drawable.play_down);
		} else if (arg1.getAction() == MotionEvent.ACTION_UP) {
			arg0.setBackgroundResource(R.drawable.play);
		}
		return false;
	}
});
```