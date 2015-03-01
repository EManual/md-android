为了最大利用手机的屏幕，我们有时候需要将标题栏等隐藏掉，使得屏幕可视面积最大化，给出一段代码，如下：
```  
//全屏
public void setFullscreen() {
	requestWindowFeature(Window.FEATURE_NO_TITLE);
	getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
			WindowManager.LayoutParams.FLAG_FULLSCREEN);
}
public void setNoTitle() {
	requestWindowFeature(Window.FEATURE_NO_TITLE);
}
```
需要注意的是：
如上方法在Activity.setContentView()之前调用，否则无效。