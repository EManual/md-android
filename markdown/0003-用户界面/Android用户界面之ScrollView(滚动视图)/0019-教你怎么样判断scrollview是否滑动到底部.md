废话不多说，直接上：
```  
scrollview.setOnTouchListener(new OnTouchListener() {
	@Override
	public boolean onTouch(View v, MotionEvent event) {
		if (event.getAction() == MotionEvent.ACTION_UP) {
			mLastY = scrollview.getScrollY();// 赋值给mLastY
			if (mLastY == (linearlayout.getHeight() - scrollview
					.getHeight())) {
				// 滑动到了底部，然后做你要做的事
			} else {
			}
		}
		return false;
	}
});
```
这里linearlayout包含在scrollview里，如果想给多个控件添加scrollview，就得把他们放进一个layout里，因为scrollview只能放一个控件在里面。这段代码是我改进了论坛里一位仁兄的，他的代码有些问题，每次点击相同的地方就会判断到底。希望对大家有所帮助~