Launcher2.2自带了屏幕标记，他是分了两块，分别为在左下角和右下角。
1) 每一块为一个imageview，在配置文件Launcher.xml中直接添加
```  
<ImageView
    android:id="@+id/previous_screen"
    android:layout_width="93dip"
    android:layout_height="20dip"
    android:layout_gravity="bottom|left"
    android:layout_marginLeft="6dip"
    android:clickable="true"
    android:focusable="true"
    android:onClick="previousScreen"
    android:scaleType="center"
    android:src="@drawable/home_arrows_left" />
```
其中android:onClick="previousScreen"引用了一个名为previousScreen的方法，在Launcher.java类中定义。
其它一些用到的配置文件及图片可以直接从2.2的工程中拷贝。
2) 在Launcher的setupViews方法中获取配置文件中添加的imageview：
```  
mPreviousView = (ImageView) dragLayer.findViewById(R.id.previous_screen);
Drawable previous = mPreviousView.getDrawable();
mPreviousView.setHapticFeedbackEnabled(false);
mPreviousView.setOnLongClickListener(this);	
```
3) 在Launcher的setupViews方法后添加previousScreen方法：
```  
public void previousScreen(View v) {
	mWorkspace.scrollLeft();
}
```
4) 在workspace的setIndicators方法中添加：
```  
mPreviousIndicator = previous;
mNextIndicator = next;
```
setCurrentScreen方法中添加：
```  
mPreviousIndicator.setLevel(mCurrentScreen);
mNextIndicator.setLevel(mCurrentScreen);
```