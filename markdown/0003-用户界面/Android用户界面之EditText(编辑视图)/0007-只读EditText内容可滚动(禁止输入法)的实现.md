使用的layout文件内容如下：
```  
<EditText
    android:id="@+id/edt_url"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:layout_marginLeft="20dip"
    android:layout_marginRight="20dip"
    android:editable="false"
    android:ellipsize="none"
    android:inputType="none"
    android:singleLine="true"
    android:text="" />
```
其中，属性android:ellipsize默认为“end”(Google的文档中未说明)，即省略掉内容的后半部分;把它置为“none”，且置属性android:singleline为true,不用设置横向滚动，即可实现文字在只读EdiText中的滚动。
另，对于只读EditText是不需要显示软键盘的。以下代码实现隐藏软键盘：
```  
private void hideIM(View edt) {
	// try to hide input_method:
	try {
		InputMethodManager im = (InputMethodManager) getSystemService(Activity.INPUT_METHOD_SERVICE);
		IBinder windowToken = edt.getWindowToken();
		if (windowToken != null) {
			// always de-activate IM
			im.hideSoftInputFromWindow(windowToken, 0);
		}
	} catch (Exception e) {
		Log.e("HideInputMethod", "failed:" + e.getMessage());
	}
}
private OnFocusChangeListener focus_listener_noIM = new OnFocusChangeListener() {
	@Override
	public void onFocusChange(View v, boolean hasFocus) {
		if (hasFocus == true) {
			hideIM(v);
		}
	}
};
private OnTouchListener touch_listener_noIM = new OnTouchListener() {

	@Override
	public boolean onTouch(View v, MotionEvent event) {
		if (event.getAction() == MotionEvent.ACTION_DOWN) {
			hideIM(v);
		}
		return false; // dispatch the event further!
	}
};
// 以下是Activity的onCreate()函数的片断：
public void onCreate(Bundle savedInstanceState) {

	EditText edt_url = (EditText) findViewById(R.id.edt_url);
	edt_url.setOnFocusChangeListener(focus_listener_noIM);
	edt_url.setOnTouchListener(touch_listener_noIM);
}
```