今天弄了一下用图片绘制开关按钮.
效果图：
![img](http://emanual.github.io/md-android/img/view_imageview/05_imageview.png) 
![img](http://emanual.github.io/md-android/img/view_imageview/05_imageview2.png) 
还有我两张start图片和stop图片就是上面的图片，到时候大家可以按照自己的图片调用..
Main.xml文件
在xml进入这段代码就ok了。
```  
<ImageView
    android:id="@+id/start"
    android:layout_width="150.px"
    android:layout_height="80.px"
    android:layout_x="120.0px"
    android:layout_y="250.0px"
    android:src="@drawable/start" />
```
Activity文件
```  
public class two extends Activity implements OnClickListener {
	private ImageView start = null; // 开始
	protected boolean isBrewing = false; // 按钮置换
	public void onCreate(Bundle savedInstanceState) {
		// 设置全屏
		super.onCreate(savedInstanceState);
		requestWindowFeature(Window.FEATURE_NO_TITLE);
		getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
				WindowManager.LayoutParams.FLAG_FULLSCREEN);
		setContentView(R.layout.two);
		// 绑定
		start = (ImageView) findViewById(R.id.start);
		start.setOnClickListener(this);
	}
	// 开始
	public void startView() {
		Bitmap bmp = BitmapFactory.decodeResource(getResources(),
				R.drawable.stop);// 打开资源图片
		start.setImageBitmap(bmp);
		isBrewing = true;
	}
	// 停止
	public void stopView() {
		Bitmap bmp = BitmapFactory.decodeResource(getResources(),
				R.drawable.start);// 打开资源图片
		start.setImageBitmap(bmp);
		isBrewing = false;
	}
	@Override
	public void onClick(View v) {
		if (v == start) {
			if (isBrewing)
				stopView();
			else
				startView();
		}
	}
}
```