在Android 2.0之后有了overridePendingTransition()，其中里面两个参数，一个是前一个activity的退出两一个activity的进入。
```  
@Override
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(R.layout.SplashScreen);
	new Handler().postDelayed(new Runnable() {
		@Override
		public void run() {
			Intent mainIntent = new Intent(SplashScreen.this,
					AndroidNews.class);
			SplashScreen.this.startActivity(mainIntent);
			SplashScreen.this.finish();
			overridePendingTransition(R.anim.mainfadein,
					R.anim.splashfadeout);
		}
	}, 3000);
}
```
上面的代码只是闪屏的一部分。
```  
getWindow().setWindowAnimations ( int );
```
这可没有上个好但是也可以 。
实现淡入淡出的效果
```  
overridePendingTransition(Android.R.anim.fade_in,android.R.anim.fade_out);
```
由左向右滑入的效果
```  
overridePendingTransition(Android.R.anim.slide_in_left,android.R.anim.slide_out_right);
```
实现zoomin和zoomout,即类似iphone的进入和退出时的效果
```  
overridePendingTransition(R.anim.zoomin, R.anim.zoomout);
```
新建animzoomin.xml文件
```  
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:Android="http://schemas.android.com/apk/res/android"
    Android:interpolator="@android:anim/decelerate_interpolator" >
    <scale
        Android:duration="@android:integer/config_mediumAnimTime"
        Android:fromXScale="2.0"
        Android:fromYScale="2.0"
        Android:pivotX="50%p"
        android:pivotY="50%p"
        android:toXScale="1.0"
        android:toYScale="1.0" />
</set>
```