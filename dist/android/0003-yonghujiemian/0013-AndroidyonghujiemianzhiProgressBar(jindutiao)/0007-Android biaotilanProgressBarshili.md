如何改变android程序的标题栏呢？
在程序的界面Activity的onCreate()函数中，setContentView(R.id.main)之前设定你的标题的样式。其中requestWindowFeature(Window.FEATURE_CUSTOM_TITLE)就是用户可以自己设定一个样式的标题栏。当然requestWindowFeature()里面还有其他的样式可以设置，自己可以看下android源码里面Window类里面的参数。
接下来自己到res/layout下面创建一个layout。
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/screen"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/left_text"
        style="?android:attr/windowTitleStyle"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:layout_alignParentLeft="true
        mce_style="?android:attr/windowTitleStyle"
        android:gravity="center_vertical"
        android:paddingLeft="5dip" />
    <TextView
        android:id="@+id/right_text"
        style="?android:attr/windowTitleStyle"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:layout_alignParentRight="true
        mce_style="?android:attr/windowTitleStyle"
        android:gravity="center_vertical"
        android:paddingRight="5dip"
        android:visibility="gone" />
    <LinearLayout
        android:id="@+id/loading_indicator"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentRight="true
        android:gravity="center_vertical"
        android:orientation="horizontal"
        android:paddingTop="2dip" >
        <TextView
            android:id="@+id/loading_text"
            style="?android:attr/windowTitleStyle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentRight="true
            mce_style="?android:attr/windowTitleStyle"
            android:gravity="center_vertical"
            android:paddingRight="5dip" />
        <ProgressBar
            android:id="@android:id/progress"
            style="?android:attr/progressBarStyleSmallTitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            mce_style="?android:attr/progressBarStyleSmallTitle"
            android:gravity="center_vertical"
            android:paddingRight="5dip" />
    </LinearLayout>
</RelativeLayout>
```
然后在setContentView(R.id.main)之后通过getWindow().setFeatureInt(Window.FEATURE_CUSTOM_TITLE,R.layout.custom_test_title)去取得这个样式。
我这次是在标题栏的右边有个progressBar。用来Loading网页的，因此我一开始不想让progressBar显示出来，那可以用findViewById(R.id.loading_indicator).setVisibility(View.INVISIBLE)设定当前不显示。(onCreate()中的代码片段)
```  
class MyWebViewClient extends WebViewClient {
	@Override
	public void onPageStarted(WebView view, String url, Bitmap favicon) {
		findViewById(R.id.loading_indicator).setVisibility(View.VISIBLE);
		super.onPageStarted(view, url, favicon);
	}
	@Override
	public void onPageFinished(WebView view, String url) {
		findViewById(R.id.loading_indicator).setVisibility(View.INVISIBLE);
		super.onPageFinished(view, url);
	}
}
```