很多不明白Activity类中包含的onSaveInstanceState和onRestoreInstanceState有什么用，首先eoeAndroid声明下使用这两个方法时一定要注意情况和了解Activity的生命周期，否则有的时候  onSaveInstanceState和onRestoreInstanceState 可能不会被触发，虽然他们都是Activity的重写方法。
他们比较常用到的地方是Sensor、Land和Port布局的自动切换，过去eoeAndroid曾经说过解决横屏和竖屏切换带来的数据被置空或者说onCreate被重复调用问题，其实Android提供的onSaveInstanceState方法可以保存当前的窗口状态在即将布局切换前或当前Activity被推入历史栈，其实布局切换也调用过onPause所以被推入Activity的history stack，如果我们的Activity在后台没有因为运行内存吃紧被清理，则切换回时会触发onRestoreInstanceState方法。
这两个方法中参数均为Bundle，可以存放类似SharedPreferences的数据，所以使用它们作为当前窗口的状态保存是比较合适的。
实际使用代码：
```  
@Override
protected void onSaveInstanceState(Bundle outState) {
	outState.putString("lastPath", "/sdcard/eoeandroid/cwj/test");
}
@Override
public void onRestoreInstanceState(Bundle savedInstanceState) {
	super.onRestoreInstanceState(savedInstanceState);
	String cwjString = savedInstanceState.getString("lastPath");
}
```