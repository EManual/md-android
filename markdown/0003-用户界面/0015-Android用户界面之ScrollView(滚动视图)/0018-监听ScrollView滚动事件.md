代码如下：
```  
horizontalScrollView1.setOnTouchListener(new OnTouchListener() {
	@Override
	public boolean onTouch(View v, MotionEvent event) {
		if (event.getAction() == MotionEvent.ACTION_MOVE) {
			// 可以监听到ScrollView的滚动事件
			Log.i("tyh",
					"ACTION_MOVE X=
							+ horizontalScrollView1.getScrollX() + "w=
							+ linearLayout1.getWidth());
		}
		return false;
	}
});
```